---
title: fgrep指令实现
categories: C语言应用代码
date: 2018-12-26 21:32:04
---
&emsp;&emsp;`fgrep`命令实现在文件中查找并打印所有包含指定字符串的文本行：<!--more-->

``` cpp
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

#define BUFFER_SIZE 512

void search ( char *filename, FILE *stream, char *string ) {
    char buffer[ BUFFER_SIZE ];

    while ( fgets ( buffer, BUFFER_SIZE, stream ) != NULL ) {
        if ( strstr ( buffer, string ) != NULL ) {
            if ( filename != NULL ) {
                printf ( "%s:", filename );
            }

            fputs ( buffer, stdout );
        }
    }
}

int main ( int ac, char **av ) {
    char *string;

    if ( ac <= 1 ) {
        fprintf ( stderr, "Usage: fgrep string file ...\n" );
        exit ( EXIT_FAILURE );
    }

    string = *++av; /* Get the string */

    if ( ac <= 2 ) { /* Process the files */
        search ( NULL, stdin, string );
    } else {
        while ( *++av != NULL ) {
            FILE *stream;
            stream = fopen ( *av, "r" );

            if ( stream == NULL ) {
                perror ( *av );
            } else {
                search ( *av, stream, string );
                fclose ( stream );
            }
        }
    }

    return EXIT_SUCCESS;
}
```