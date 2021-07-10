---
title: "哈希表"
date: 2021-07-10T13:57:15+08:00
draft: false
categories: ["数据结构"]
enableMathJax: false
---

# 简介
链表散列法的哈希表本质上是一个元素为链表的数组。key值通过哈希函数转化成一个特定值，安插进数组的特定位置，当查找时，只需要计算出哈希值，就可以立刻定位到数组相对应的位置，从而实现时间复杂度为O(1)的查找。当出现计算结果一样的key值时，对应位置的链表增长。
```C
//成员链表的结构定义，包含了要储存的内容
typedef struct hash_ele{
    int key;
    int value;
    struct hash_ele* next;
} hash_ele;

//数组的定义，成员为指向链表的指针
typedef struct hash_array{
    hash_ele* arr[30];
} hash_array;

//哈希函数的一个示例，该函数让key值对29取模
int hashCode(int key){
    int index = key % 29;
    return index;
}
```

> hash_array的用法：  
> hash_array的成员为`hash_ele* arr[30]`，原因是哈希函数为key值对29取模，因此数组下标可取值为0~29，长度最高为30。  
> 结构定义方式也可以使用`hash_ele** arr`  
> 当`hashCode(key)`计算出值时(假设值为index)，将元素插入`arr -> arr[index]`

***由于成员为链表，操作方面和链表几乎一样，接下来仅记录实现过程中遇到的问题***
# 注意事项
1. 上述的结构体定义方式，在创建时需要使用calloc申请内存空间并初始化。如果使用malloc，将不会进行初始化，使用时会遇到segmentation fault。  

    ```C
    //创建数组，由于成员为hash_ele*且长度为30，因此申请30*sizeof(hash_ele*)的空间并初始化
    hash_array* hashArrayCreate(){
        hash_array* arr = (hash_array*)calloc(30, sizeof(hash_ele*));
        return arr;
    }

    //创建元素，由于创建后会进行赋值，因此不需要使用calloc进行初始化
    hash_ele* hashElementCreate(int key, int value){
        hash_ele* element = (hash_ele*)malloc(sizeof(hash_ele));
        element -> key = key;
        element -> value = value;
        element -> next = NULL;
        return element;
    }
    ```
2. 在创建并插入元素之前，应先使用search函数查找该key值是否存在。

    ```C
    void hashUpdate(hash_array* arr, int key, int value){
        //判断是否存在，若存在，更新值
        hash_ele* judge = hashSearch(arr, key);
        if(judge != NULL){
            printf("element is already exist! update it.\n");
            judge -> value = value;
            return;
        }
        hash_ele* element = hashElementCreate(key, value);
        int index = hashCode(key);
        hash_ele* ptr = arr -> arr[index];
        if(ptr == NULL){
            arr -> arr[index] = element;
        }
        else{
            element -> next = ptr;
            arr -> arr[index] = element;
        }
    }
    ```