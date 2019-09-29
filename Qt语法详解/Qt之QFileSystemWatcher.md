---
title: Qt之QFileSystemWatcher
categories: Qt语法详解
abbrlink: 21a9b68
date: 2019-01-24 13:40:43
---
### 简述

&emsp;&emsp;`QFileSystemWatcher`类用于提供监视文件和目录修改的接口。它通过监控指定路径的列表，监视文件系统中文件和目录的变更。调用`addPath`函数可以监控一个特定的文件或目录。如果需要监控多个路径，可以使用`addPaths`。通过使用`removePath`和`removePaths`函数来移除现有路径。
&emsp;&emsp;`QFileSystemWatcher`检查添加到它的每个路径，已添加到`QFileSystemWatcher`的文件可以使用的`files`函数进行访问，目录则使用`directories`函数进行访问。当一个文件被修改、重命名或从磁盘上删除时，会发出`fileChanged`信号。同样，当一个目录或它的内容被修改或删除时，会发射`directoryChanged`信号。需要注意，文件一旦被重命名或从硬盘删除，目录从磁盘上删除时，`QFileSystemWatcher`将停止监控。监控文件和目录进行修改的行为会消耗系统资源，这意味着进程同时监控的文件数量是有限制的。一些系统限制打开的文件描述符的数量默认为`256`，也就是说如果进程使用`addPath`和`addPaths`函数添加超过`256`个文件或目录到文件系统将会失败。

### 公共函数

- `bool addPath(const QString & path)`：如果路径存在，则添加至文件系统监控；如果路径不存在或者已经被监控了，那么不添加。如果路径是一个目录，内容被修改或删除时，会发射`directoryChanged`信号；否则当文件被修改、重命名或从磁盘上删除时，会发出`fileChanged`信号。如果监控成功，返回`true`，否则返回`false`。监控失败的原因通常依赖于系统，但也包括资源不存在、接入失败、或总的监控数量限制等原因。
- `QStringList addPaths(const QStringList & paths)`：添加每一个路径至文件系统监控，如果路径不存在或者已经被监控了，那么不添加。返回值是不能被监控的路径列表。
- `QStringList directories() const`：返回一个被监控的目录路径列表。
- `QStringList files() const`：返回一个被监控的文件路径列表。
- `bool removePath(const QString & path)`：从文件系统监控中删除指定的路径。如果监控被成功移除，返回`true`。删除失败的原因通常是与系统相关，但可能是由于路径已经被删除。
- `QStringList removePaths(const QStringList & paths)`：从文件系统监控中删除指定的路径。返回值是一个无法删除成功的路径列表。

### 信号

- `void directoryChanged(const QString & path)`：当目录被修改(例如在指定的路径中添加或删除一个文件)或从磁盘删除时，这个信号将被发射。注意，如果在短时间内有几种变化，可能有些变化不会发出这个信号。然而，在变化的序列中，最后的变化总会发射这个信号。这是一个私有信号，可以用于信号连接但不能由用户发出。
- `void fileChanged(const QString & path)`：当在指定路径中的文件被修改、重命名或从磁盘上删除时，这个信号将被发射。这是一个私有信号，可以用于信号连接但不能由用户发出。

### 示例

&emsp;&emsp;下面来实现一个`文件/目录`监控的类。`FileSystemWatcher.h`如下：

``` cpp
#ifndef FILE_SYSTEM_WATCHER_H
#define FILE_SYSTEM_WATCHER_H
​
#include <QObject>
#include <QMap>
#include <QFileSystemWatcher>
​
class FileSystemWatcher : public QObject {
    Q_OBJECT
public:
    static void addWatchPath ( QString path );
public slots:
    /* 目录更新时调用，path是监控的路径 */
    void directoryUpdated ( const QString &path );
    /* 文件被修改时调用，path是监控的路径 */
    void fileUpdated ( const QString &path );
private:
    explicit FileSystemWatcher ( QObject *parent = 0 );
private:
    static FileSystemWatcher *m_pInstance;
    QFileSystemWatcher *m_pSystemWatcher;
    /* 当前每个监控的内容目录列表 */
    QMap<QString, QStringList> m_currentContentsMap;
};
​
#endif
```

&emsp;&emsp;`FileSystemWatcher.cpp`如下：

``` cpp
#include <QDir>
#include <QFileInfo>
#include <qDebug>
#include "FileSystemWatcher.h"
​
FileSystemWatcher *FileSystemWatcher::m_pInstance = NULL;
​
FileSystemWatcher::FileSystemWatcher ( QObject *parent ) : QObject ( parent ) {
}
​
void FileSystemWatcher::addWatchPath ( QString path ) { /* 监控文件或目录 */
    qDebug() << QString ( "Add to watch: %1" ).arg ( path );
​
    if ( m_pInstance == NULL ) {
        m_pInstance = new FileSystemWatcher();
        m_pInstance->m_pSystemWatcher = new QFileSystemWatcher();
        /* 连接QFileSystemWatcher的directoryChanged和fileChanged信号到相应的槽 */
        connect ( m_pInstance->m_pSystemWatcher, SIGNAL ( directoryChanged ( QString ) ), \
                  m_pInstance, SLOT ( directoryUpdated ( QString ) ) );
        connect ( m_pInstance->m_pSystemWatcher, SIGNAL ( fileChanged ( QString ) ), \
                  m_pInstance, SLOT ( fileUpdated ( QString ) ) );
    }
​
    m_pInstance->m_pSystemWatcher->addPath ( path ); /* 添加监控路径 */
    QFileInfo file ( path ); /* 如果添加路径是一个目录，保存当前内容列表 */
​
    if ( file.isDir() ) {
        const QDir dirw ( path );
        m_pInstance->m_currentContentsMap[path] = \
            dirw.entryList ( QDir::NoDotAndDotDot | QDir::AllDirs | QDir::Files, QDir::DirsFirst );
    }
}
​
/* 只要任何监控的目录更新(添加、删除、重命名)，就会调用 */
void FileSystemWatcher::directoryUpdated ( const QString &path ) {
    qDebug() << QString ( "Directory updated: %1" ).arg ( path );
    /* 比较最新的内容和保存的内容找出区别(变化) */
    QStringList currEntryList = m_currentContentsMap[path];
    const QDir dir ( path );
    QStringList newEntryList = \
        dir.entryList ( QDir::NoDotAndDotDot | QDir::AllDirs | QDir::Files, QDir::DirsFirst );
    QSet<QString> newDirSet = QSet<QString>::fromList ( newEntryList );
    QSet<QString> currentDirSet = QSet<QString>::fromList ( currEntryList );
    /* 添加了文件 */
    QSet<QString> newFiles = newDirSet - currentDirSet;
    QStringList newFile = newFiles.toList();
    /* 文件已被移除 */
    QSet<QString> deletedFiles = currentDirSet - newDirSet;
    QStringList deleteFile = deletedFiles.toList();
    /* 更新当前设置 */
    m_currentContentsMap[path] = newEntryList;
​
    if ( !newFile.isEmpty() && !deleteFile.isEmpty() ) {
        /* 文件/目录重命名 */
        if ( ( newFile.count() == 1 ) && ( deleteFile.count() == 1 ) ) {
            qDebug() << QString ( "File Renamed from %1 to %2" ).arg (
                deleteFile.first() ).arg ( newFile.first() );
        }
    } else {
        /* 添加新文件/目录至Dir */
        if ( !newFile.isEmpty() ) {
            qDebug() << "New Files/Dirs added: " << newFile;
​
            foreach ( QString file, newFile ) {
                /* 处理操作每个新文件 */
            }
        }
​
        /* 从Dir中删除文件/目录 */
        if ( !deleteFile.isEmpty() ) {
            qDebug() << "Files/Dirs deleted: " << deleteFile;
​
            foreach ( QString file, deleteFile ) {
                /* 处理操作每个被删除的文件 */
            }
        }
    }
}
​
void FileSystemWatcher::fileUpdated ( const QString &path ) { /* 文件修改时调用 */
    QFileInfo file ( path );
    QString strPath = file.absolutePath();
    QString strName = file.fileName();
    qDebug() << QString ( "The file %1 at path %2 is updated" ).arg ( strName ).arg ( strPath );
}
```

测试代码如下：

``` cpp
#include "FileSystemWatcher.h"
​
int main ( int argc, char *argv[] ) {
    QApplication a ( argc, argv );
    FileSystemWatcher::addWatchPath ( "E:/Test" );
    return a.exec();
}
```