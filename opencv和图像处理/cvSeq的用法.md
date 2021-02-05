---
title: cvSeq的用法
categories: opencv和图像处理
date: 2018-12-30 18:13:46
---
### cvCreateSeq

&emsp;&emsp;该函数的功能是创建一个序列：<!--more-->

``` cpp
CvSeq *cvCreateSeq (int seq_flags, int header_size, int elem_size, CvMemStorage *storage);
```

- `seq_flags`：它是序列的符号标志。如果序列不会被传递给任何使用特定序列的函数，那么将它设为`0`，否则从预定义的序列类型中选择一合适的类型。
- `header_size`：它是序列头部的大小，其值必须大于或等于`sizeof(CvSeq)`。如果制定了类型或它的扩展名，则此类型必须适合基类的头部大小。
- `elem_size`：它是元素的大小，以字节计算。这个大小必须与序列类型(由`seq_flags`指定)相一致。例如对于一个点的序列，元素类型`CV_SEQ_ELTYPE_POINT`应当被指定，参数`elem_size`必须等同于`sizeof(CvPoint)`。
- `storage`：它指向前面定义的内存存储器。

### cvCloneSeq

&emsp;&emsp;该函数的作用是创建序列的一份拷贝：

``` cpp
CvSeq *cvCloneSeq ( const CvSeq *seq, CvMemStorage *storage = NULL );
```

### cvSeqInvert

&emsp;&emsp;该函数的作用是将序列中的元素进行逆序操作：

``` cpp
void cvSeqInvert ( CvSeq *seq );
```

### cvSeqSort

&emsp;&emsp;该函数的功能是使用特定的比较函数对序列中的元素进行排序：

``` cpp
void cvSeqSort ( CvSeq *seq, CvCmpFunc func, void *userdata = NULL );
```

### cvSeqSearch

&emsp;&emsp;该函数的功能是查询序列中的元素：

``` cpp
char *cvSeqSearch (
    CvSeq *seq, const void *elem, CvCmpFunc func,
    int is_sorted, int *elem_idx, void *userdata = NULL);
```

### cvClearSeq

&emsp;&emsp;其函数的功能是清空序列：

``` cpp
void cvClearSeq ( CvSeq *seq );
```

### cvSeqPush

&emsp;&emsp;该函数的功能是添加元素到序列的尾部：

``` cpp
char *cvSeqPush ( CvSeq *seq, void *element = NULL );
```

### cvSeqPop

&emsp;&emsp;该函数的作用是删除序列尾部元素：

``` cpp
void cvSeqPop ( CvSeq *seq, void *element = NULL );
```

### cvSeqPushFront

&emsp;&emsp;该函数的作用是在序列头部添加元素：

``` cpp
char *cvSeqPushFront ( CvSeq *seq, void *element = NULL );
```

### cvSeqPopFront

&emsp;&emsp;其函数的作用是删除在序列的头部的元素：

``` cpp
void cvSeqPopFront ( CvSeq *seq, void *element = NULL );
```

### cvSeqPushMulti

&emsp;&emsp;其函数的作用是添加多个元素到序列尾部或头部：

``` cpp
void cvSeqPushMulti ( CvSeq *seq, void *elements, int count, int in_front = 0 );
```

### cvSeqPopMulti

&emsp;&emsp;其函数作用是删除多个序列头部或尾部元素：

``` cpp
void cvSeqPopMulti ( CvSeq *seq, void *elements, int count, int in_front = 0 );
```

### cvSeqInsert

&emsp;&emsp;该函数的作用是在序列中的指定位置添加元素：

``` cpp
char *cvSeqInsert ( CvSeq *seq, int before_index, void *element = NULL );
```

### cvSeqRemove

&emsp;&emsp;其函数的作用是删除序列中的指定位置的元素：

``` cpp
void cvSeqRemove ( CvSeq *seq, int index );
```

### cvGetSeqElem

&emsp;&emsp;其函数的作用是返回索引所指定的元素指针：

``` cpp
char *cvGetSeqElem ( const CvSeq *seq, int index );
```

### cvSeqElemIdx

&emsp;&emsp;其函数的作用是返回序列中元素的索引：

``` cpp
int cvSeqElemIdx ( const CvSeq *seq, const void *element, CvSeqBlock **block = NULL );
```

### cvStartAppendToSeq

&emsp;&emsp;其函数的作用是将数据写入序列中，并初始化该过程：

``` cpp
void cvStartAppendToSeq ( CvSeq *seq, CvSeqWriter *writer );
```

### cvStartWriteSeq

&emsp;&emsp;其函数的作用是创建新序列，并初始化写入部分：

``` cpp
void cvStartWriteSeq (
    int seq_flags, int header_size, int elem_size,
    CvMemStorage *storage, CvSeqWriter *writer);
```

### cvEndWriteSeq

&emsp;&emsp;其函数的作用是完成写入操作：

``` cpp
CvSeq *cvEndWriteSeq ( CvSeqWriter *writer );
```

### cvStartReadSeq

&emsp;&emsp;其函数的作用是初始化序列中的读取过程：

``` cpp
void cvStartReadSeq ( const CvSeq *seq, CvSeqReader *reader, int reverse = 0 );
```


---

&emsp;&emsp;一直困惑于`CvSeq`到底是个什么样的东西，因为曾经拿到别人写的一个函数库，其返回值是一个`CvSeq`指针。我的任务是遍历所有的`Sequence`，然后删除其中不符合要求的`Sequence`。由于没有文档，我当时并不知道需要遍历的是`Sequence`还是`Sequence`中的`Element`，于是写下了类似如下的代码：

``` cpp
CvSeq *pCurSeq = pInputSeq;
int index = 0;

while ( pCurSeq = pCurSeq->h_next ) {
    if ( process ( pCurSeq ) ) {
        pCurSeq = pCurSeq->h_prev; /* 这里为了简单，不考虑是否为列表头 */
        cvSeqRemove ( pInputSeq, index );
        --index;
    }

    ++index;
}
```

事实证明这段代码是错误的，而且返回的错误信息是：

``` bash
> OpenCV ERROR: One of arguments' values is out of range (Invalid index)
> in function cvSeqRemove, cxdatastructs.cpp(1587)
```

为什么会有这样的错误呢？看一下`CvSeq`的源代码就清楚了。下面是`OpenCV 1.0`版本有关`CvSeq`的定义：

``` cpp
#define CV_TREE_NODE_FIELDS(node_type)                      \
    int    flags;             /* micsellaneous flags     */ \
    int    header_size;       /* size of sequence header */ \
    struct node_type* h_prev; /* previous sequence       */ \
    struct node_type* h_next; /* next sequence           */ \
    struct node_type* v_prev; /* 2nd previous sequence   */ \
    struct node_type* v_next  /* 2nd next sequence       */

/*
 * Read/Write sequence.
 * Elements can be dynamically inserted to or deleted from the sequence.
 */
#define CV_SEQUENCE_FIELDS()                                   \
    CV_TREE_NODE_FIELDS(CvSeq);                                \
    int   total;       /* total number of elements */          \
    int   elem_size;   /* size of sequence element in bytes */ \
    char* block_max;   /* maximal bound of the last block */   \
    char* ptr;         /* current write pointer */             \
    int   delta_elems; /* how many elements allocated when the seq grows */ \
    CvMemStorage* storage;   /* where the seq is stored */                  \
    CvSeqBlock* free_blocks; /* free blocks list */                         \
    CvSeqBlock* first; /* pointer to the first sequence block */

typedef struct CvSeq {
    CV_SEQUENCE_FIELDS()
} CvSeq;
```

&emsp;&emsp;原来`CvSeq`本身就是一个可增长的序列，`CvSeq::total`是指序列内部有效元素的个数；而`h_next`和`h_prev`并不是指向`CvSeq`内部元素的指针，它们是指向其它`CvSeq`的。再回到最初的代码，可以看到该代码具有逻辑上的错误，首先`while`语句遍历的是所有的`CvSeq`，使用`process`处理每一个`CvSeq`，而遇到需要删除的`CvSeq`时，又使用`cvSeqRemove`删除当前`CvSeq`中的第`index`个元素。实际上此时`index`很可能超出了当前`CvSeq`中总元素的个数，所以出现了超出边界的错误。正确的做法是直接删除该`CvSeq`：

``` cpp
CvSeq *pCurSeq = pInputSeq;
CvSeq *pOldSeq = NULL;

while ( pCurSeq ) {
    if ( process ( pCurSeq ) ) {
        pOldSeq = pCurSeq;

        if ( pOldSeq->h_prev ) {
            pCurSeq = pOldSeq->h_prev;
            pCurSeq->h_next = pOldSeq->h_next;
            pOldSeq->h_next->h_prev = pCurSeq;
            pCurSeq = pCurSeq->h_next;
            cvClearSeq ( pOldSeq );
        } else {
            pCurSeq = pOldSeq->h_next;
            pCurSeq->h_prev = NULL;
            cvClearSeq ( pOldSeq );
        }
    } else {
        pCurSeq = pCurSeq->h_next;
    }
}
```

&emsp;&emsp;后来在`Google Book`里查了一下，发现`Learning OpenCV: Computer Vision with the OpenCV Library`中有这么一段话：The sequence structure itself has some important elements that you should be aware of. The first, and one you will use often, is total. This is the total number of points or objects in the sequence. The next four important elements are pointers to other sequence: `h_prev`, `h_next`, `v_prev` and `v_next`. These four pointers are part of what are called `CV_TREE_NODE_FIELDS`; they are used not to indicate elements inside of the sequence but rather to connect different sequences to one another. Other objects in the `OpenCV` universe also contain these tree node fields。