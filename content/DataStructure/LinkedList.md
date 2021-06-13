---
title: "设计一个链表"
date: 2021-06-13T21:29:43+08:00
draft: false
categories: ["数据结构"]
enableMathJax: false
---

> 链表可以理解为一个“按顺序保存一系列数据的容器”。除了用来记录，还要有“操作”这些数据的能力。因此我们使用一系列函数来对链表进行增加、删除等操作。  
> 对于用户，只需要提供如何修改链表的函数接口；而在内部，为了更方便地操作链表，我们需要定义出链表的`长度`、`头结点`、`尾结点`等信息，并随着用户的操作进行维护。以整数单链表为例：  
> ```C
> //用来记录实际链表数据
> typedef struct LinkedList{
>     int val;
>     struct LinkedList* next;
> } ListNode;
> //用来记录链表头、尾结点以及长度信息
> typedef struct {
>     ListNode* head;
>     ListNode* tail;
>     unsigned int length;
> } MyLinkedList;
> ```
# 创建链表
```C
//当创建一个新链表时，需要创建一个用来维护链表的数据
MyLinkedList* myLinkedListCreate() {
//分配空间并将头结点和尾结点设为NULL，长度为0
    MyLinkedList* list = (MyLinkedList*)malloc(sizeof(MyLinkedList));
    list -> head = NULL;
    list -> tail = NULL;
    list -> length = 0;
//返回指针以供函数使用
    return list;
}
```
# 为链表添加元素
先定义一个创建新结点的函数供需要添加结点时使用  
```C
//为链表创建新元素时，提供该元素数据来创建一个结点指向NULL的新结点
ListNode* ListNodeCreate(int val){
    ListNode* node = (ListNode*)malloc(sizeof(ListNode));
    node -> val = val;
    node -> next = NULL;
    return node;
}
```
用户使用时并不会考虑结点是如何“插入”链表内的，用户只需要考虑在什么地方插入需要的结点。但程序实现上需要将`头插`、`尾插`和`中间插`区分开来，并根据用户指定的“在何处插入”来判断使用哪种方式。
## 头插法
```C
void myLinkedListAddAtHead(MyLinkedList* obj, int val) {
    //使用了创建结点的函数，之后不再赘述
    ListNode* new_head = ListNodeCreate(val);
    //定义新头结点
    new_head -> next = obj -> head;
    //如果是空链表，则同时将第一个结点定义为尾结点
    if(obj -> head == NULL){
        obj -> tail = new_head;
    }
    //更改头结点为新结点
    obj -> head = new_head;
    //最后记得维护链表长度
    obj -> length++;
}
```
## 尾插法
```C
void myLinkedListAddAtTail(MyLinkedList* obj, int val) {
    ListNode* new_tail = ListNodeCreate(val);
    //如果是空链表并被指定了尾插（虽然一般不会出现这种情况），则定义头尾结点
    if(obj -> head == NULL && obj -> tail == NULL){
        obj -> head = new_tail;
        obj -> tail = new_tail;
    }
    //让原本的尾结点指向新尾结点，并更改尾结点数据
    else{
        obj -> tail -> next = new_tail;
        obj -> tail = new_tail;
    }
    //维护长度
    obj -> length++;
}
```
## 中间插
和数列相同，链表的第一个结点序号为`0`。因此索引值`index`也最好代表需要插入的位置的序号。如果需要面向用户，则在外部程序中引入新变量让其等于`index + 1`就好了。  
同时记得维护长度
```C
void myLinkedListAddAtIndex(MyLinkedList* obj, int index, int val) {
    //索引小于0，错误
    if(index < 0){
        printf("error!\n");
        return;
    }
    //索引等于0，头插
    else if(index == 0){
        myLinkedListAddAtHead(obj, val);
        return;
    }
    //索引等于长度，尾插
    else if(index == obj -> length){
        myLinkedListAddAtTail(obj, val);
        return;
    }
    //索引大于长度，不做任何事，也可以返回一个错误
    else if(index > obj -> length){
        return;
    }
    //分类讨论结束后，最后的情况为从中间某个结点插入
    else{
        ListNode* new_node = ListNodeCreate(val);
        ListNode* pre = obj -> head;
        //使指针遍历到要插入的结点的上一个结点
        for(int i = 0; i < index - 1; i++){
            pre = pre -> next;
        }
        //将pre结点指向的结点与pre结点断开，然后重新连接成pre->new_node->aft
        ListNode* aft = pre -> next;
        pre -> next = new_node;
        new_node -> next = aft;
        obj -> length++;
    }
}
```
# 删除结点
> 与插入结点一样，删除结点也需要考虑是否是删除头尾结点。  
> 需要注意每次删除结点后，都要释放掉删除的结点的内存。
```C
void myLinkedListDeleteAtIndex(MyLinkedList* obj, int index) {
    //如果索引不在这个链表所有序号的集合中，报错
    if(index < 0 || index >= obj -> length){
        printf("error! cannot delete!\n");
        return;
    }
    //删除头部，直接将头部右移
    else if(index == 0){
        ListNode* free_head = obj -> head;
        obj -> head = obj -> head -> next;
        free(free_head);
        obj -> length--;
        return;
    }
    //删除尾部，将尾部指针左移，方法为遍历到尾结点的前一个结点，然后定义其为尾结点
    else if(index == obj -> length - 1){
        ListNode* ptr = obj -> head;
        ListNode* free_tail = obj -> tail;
        while(ptr -> next != obj -> tail){
            ptr = ptr -> next;
        }
        ptr -> next = NULL;
        obj -> tail = ptr;
        free(free_tail);
        obj -> length--;
    }
    //删除中间结点，将要删除的结点的左节点的next指向它的右结点即可
    else{
        ListNode* ptr = obj -> head;
        for(int i = 0; i < index-1; i++){
        ptr = ptr -> next;
        }
        ListNode* free_del = ptr -> next;
        ListNode* aft = ptr -> next -> next;
        ptr -> next = aft;
        free(free_del);
        obj -> length--;
    }
}
```
# 释放内存
链表使用完之后，及时将它的马飞掉
```C
void myLinkedListFree(MyLinkedList* obj) {
    ListNode* ptr = obj -> head;
    while(obj -> head != NULL){
        ptr = obj -> head;
        obj -> head = obj -> head -> next;
        free(ptr);
    }
    free(obj);
}
```
# 实例测试
为了验证写的这些功能是否正确，写一个main函数来跑跑看测试一下。  
比如：
```C
//写一个打印链表的函数
void printMyLinkedList(MyLinkedList* obj){
    ListNode* ptr = obj -> head;
    for(int i = 0; i < obj -> length; i++){
        printf("%d ", ptr -> val);
        ptr = ptr -> next;
    }
}
int main(){
    //从上到下依次把注释去掉，运行一次，观察长度、头尾节点、打印链表的输出，来判断函数是否正常工作
    MyLinkedList* obj = myLinkedListCreate();
    myLinkedListAddAtHead(obj, 2);
    //myLinkedListDeleteAtIndex(obj, 1);
    //myLinkedListAddAtHead(obj, 2);
    //myLinkedListAddAtHead(obj, 7);
    //myLinkedListAddAtHead(obj, 3);
    //myLinkedListAddAtHead(obj, 2);
    //myLinkedListAddAtHead(obj, 5);
    //myLinkedListAddAtTail(obj, 5);
    //int a = myLinkedListGet(obj, 5);printf("get: %d\n", a);
    //myLinkedListDeleteAtIndex(obj, 6);
    //myLinkedListDeleteAtIndex(obj, 4);
    printf("length: %d\n", obj -> length);
    printf("head: %d, tail: %d\n", obj -> head -> val, obj -> tail -> val);
    printMyLinkedList(obj);
    myLinkedListFree(obj);
    return 0;
}
```