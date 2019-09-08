---
title: C语言实现消息队列
date: 2019-02-04 12:34:58
categories: C语言应用代码
---
&emsp;&emsp;`GM_Queue.h`如下：

``` cpp
/* 采用链表实现，链表的头部为队首，链表的尾部为队尾，
   Enqueue在队尾进行操作，Dequeue在队首进行操作 */
#ifndef _GM_QUEUE_H
#define _GM_QUEUE_H
​
#include <stdlib.h>
​
#ifdef __cplusplus
extern"C" {
#endif
​
int GM_Queue_Enqueue ( int value ); /* 加入队列 */
int GM_Queue_Dequeue ( int *value ); /* 离开队列 */
void GM_Queue_Clear(); /* 清除队列 */
int GM_Queue_Length(); /* 计算队列长度 */
​
#ifdef __cplusplus
}
#endif /* __cplusplus */
​
#endif /* _GM_QUEUE_H */
```

&emsp;&emsp;`GM_Queue.c`如下：

``` cpp
#include "GM_Queue.h"
#include <stdio.h>
​
typedef struct Queue {
    int value;
    struct Queue *next;
} Queue_Struct;
​
static Queue_Struct *head = NULL;
static Queue_Struct *tail = NULL;
static int count = 0;
​
int GM_Queue_Enqueue ( int value ) {
    Queue_Struct *tmp = ( Queue_Struct * ) malloc ( sizeof ( Queue_Struct ) );
​
    if ( NULL == tmp ) {
        return -1;
    }
​
    tmp->value = value;
    tmp->next  = NULL;
​
    if ( NULL == tail ) {
        head = tmp;
    } else {
        tail->next = tmp;
    }
​
    tail = tmp;
    ++count;
    return 1;
}
​
int GM_Queue_Dequeue ( int *value ) {
    Queue_Struct *tmp = NULL;
​
    if ( ( NULL == head ) || ( NULL == value ) ) {
        return -1;
    }
​
    *value = head->value;
    tmp = head;
​
    if ( head == tail ) {
        head = NULL;
        tail = NULL;
    } else {
        head = head->next;
    }
​
    free ( tmp );
    tmp = NULL;
    --count;
    return 1;
}
​
void GM_Queue_Clear ( void ) {
    int i = 0;
    int value = 0;
​
    while ( count > 0 ) {
        GM_Queue_Dequeue ( &value );
    }
}
​
int GM_Queue_Length ( void ) {
    return count;
}
​
void main ( void ) {
    int i = 0;
    int rt = -1;
    int value = 0;
​
    for ( i = 0; i < 10; ++i ) {
        rt = GM_Queue_Enqueue ( i );
        printf ( "ENQUEUE rt = %d: value = %d\n", rt, i );
    }
​
    printf ( "COUNT = %d\n", GM_Queue_Length() );
​
    for ( i = 0; i < 10; ++i ) {
        rt = GM_Queue_Dequeue ( &value );
        printf ( "DEQUEUE rt = %d: value = %d\n", rt, value );
    }
​
    rt = GM_Queue_Dequeue ( &value );
    printf ( "DEQUEUE rt = %d: value = %d\n", rt, value );
​
    for ( i = 0; i < 10; ++i ) {
        rt = GM_Queue_Enqueue ( i );
        printf ( "ENQUEUE rt = %d: value = %d\n", rt, i );
    }
​
    GM_Queue_Clear();
    printf ( "COUNT = %d\n", GM_Queue_Length() );
}
```