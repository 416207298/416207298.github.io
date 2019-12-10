---
title: 你想要了解的单链表
date: 2018-10-31 16:30:52
tags: 
- 算法
- 数据结构
- 单链表
categories:
- Data Structure
---

## 为什么要说链表
链表这个词想必大家都很熟悉，但在实际工作中，我发现虽然经常会用到，但如果真是让自己从头开始造轮子，真正的从零开发，却还是会有种无从下手的感觉。如果你和我一样，请继续看下去。
<!-- more -->
本文主要会讲链表的创建，销毁和遍历等链表的基本操作，并以几道面试题为例来做更具体的说明。
## 开始创建链表
关于链表和数组的区别与各自的优缺点在此就不细说了，随便网上一抓一大把，今天主要弄些实在的，下面先从如何创建一个链表开始。
链表说白了就是从一个头指针开始，依次往后顺延连接的一个指针链。所以想获取一个链表只需要知道它的头指针即可，下面的是创建一个链表的代码。
``` C
typedef struct ListNode {
    int m_value;
    ListNode *p_next;
    ListNode(int x): m_value(x), p_next(NULL) { }
}Link;

/* 根据n个元素的数组arr创建一个链表，并返回链表头 */
Link *createALinklist(int arr[], int n)
{
    if (n <= 0)
        return NULL;
    Link *head = new Link(arr[0]);
    Link *cur_node = head;
    for (int i = 1; i < n; ++i)
    {
        cur_node->p_next = new Link(arr[i]);
        cur_node = cur_node->p_next;
    }
    return head;
}
```
## 遍历链表
知道了头指针，很方便地就可以遍历整个链表内容；
``` C
/* 打印以head为头的链表节点信息 */
void traverseList(Link *head)
{
    if (head == NULL)
        return;
    else
    {
        Link *cur_node = head;
        for (; cur_node != NULL; cur_node = cur_node->p_next)
            cout << cur_node->m_value << " ";
        cout << endl;
    }
}
```
## 销毁链表内容
在释放链表空间时要注意一个顺序问题，因为如果释放操作在指针指向下一个节点之前，那么就无法找到接下来的节点；故为了保证能正确释放，需要用到一个新指针，保存当前节点的位置。
``` C
/* 销毁以head为头的链表空间 */
void destoryALinklist(Link *head)
{
    if (head == NULL)
        return;
    Link *cur_node = head;
    while (cur_node != NULL)
    {
        Link *delNode = cur_node;
        cur_node = cur_node->p_next;  // 要注意删除前要先将当前节点指针指向下一个位置
        delete delNode;
    }
    return;
}
```
## 添加链表节点
在链表的结尾添加一个新节点，这也属于链表的基本操作之一。
但需要注意的是：
* 链表为空时，要直接把头节点指向新节点；
* 要想在函数内部改变指针本身，需要用到二级指针

``` C
/* 在链表结尾添加一个节点 */
bool addOneNodeToTail(int item, Link **head)  // **是为了要改变head指向的地址
{
    Link *cur = *head;
    Link *pnew = new Link(item);
    if (pnew == NULL)  // 内存申请失败
        return false;

    if (cur == NULL)  // 链表为空时
        *head = pnew;
    else
    {
        while (cur->p_next != NULL)
            cur = cur->p_next;
        cur->p_next = pnew;
    }
    return true;

}
```
## 面试题：删除链表节点
![面试题13](https://blog-pics.nos-eastchina1.126.net/jianzhioffer13.png)
### 常规删除节点的方法
要想删除链表的一个节点，首先要遍历找到前一个节点的位置，然后再删除该节点；
如有链表`a--b--c--d--null`，要删除c节点，则需要遍历到c的前一个节点b，把b的next指针指向d，再释放c，即完成了删除操作；
但这样有n个节点的时间复杂度是O(n)，不符合题目的要求；
### 符合题目要求的方法
如想删除c节点，不一定要遍历到其前一个节点b，有了c节点的指针，可以很方便地获取到其下一个节点d，而删除操作就相当于是：
* 把d节点的内容赋给c节点
* 把c节点的next指针指向d的next

以上操作就无需遍历到c的上一个节点，此操作的时间复杂度是O(1)，符合题目的要求；
但需要注意两种特殊情况：
1. 链表只有一个节点，此时操作就变成了把头节点置null，并释放空间；
2. 要删除节点是链表的最后一个节点，由于没有后一个节点，此时需用常规删除节点的方法进行删除；

代码如下：
``` C
/* 删除指针指向的链表节点 */
bool deleteOneNode(Link *head, Link *toBeDeleted)
{
    if (!head || !toBeDeleted)
        return false;
    // 链表只有一个节点
    if (head->p_next == NULL)
    {
        delete toBeDeleted;
        toBeDeleted = NULL;
        head = NULL;
    }
    // 要删除的节点是尾节点
    else if (toBeDeleted->p_next == NULL)
    {
        Link *cur_node = head;
        while (cur_node != NULL)
        {
            if (cur_node->p_next != toBeDeleted)
                cur_node = cur_node->p_next;
            else
            {
                cur_node ->p_next = NULL;
                delete toBeDeleted;
                toBeDeleted = NULL;
            }
        }
    }
    else
    {
        // 删除中间的节点
        toBeDeleted->m_value = toBeDeleted->p_next->m_value;
        toBeDeleted->p_next = toBeDeleted->p_next->p_next;
        delete toBeDeleted->p_next;
    }
    return true;
}
```
测试程序
``` C
int main()
{
    int arr[] = {1,2,3,4,5};
    Link *head = createALinklist(arr, 5);
    Link *del_node = head;
    traverseList(head);
    for (int i = 0; i < 3; ++i)
    {
        del_node = del_node->p_next;
    }
    deleteOneNode(head, del_node);
    traverseList(head);
    destoryALinklist(head);

    return 0;
}
```
输出结果
![运行结果](https://blog-pics.nos-eastchina1.126.net/result.png)

## 面试题：反转链表
![面试题16](https://blog-pics.nos-eastchina1.126.net/jianzhioffer16.jpg)
### 反转链表的方法
很简单，把后一个节点的next指针指向前一个节点就好了；
但是需要注意：
* 反转之后头节点成为尾节点，后面是`NULL`；
* 容易出现链表的断裂；
* 当链表为空时，要做健壮性处理。

综上，为了链表不断裂，需要三个指针来做这件事，一个指向当前节点，一个是它的上一节点，一个是下个节点，即`cur，pre，next`。我们有了这三个指针，接下来的事就好办了，只需从头遍历链表，把每个节点的next指向pre即可。
代码如下：
``` C
Link *reverseList(Link *head)
{
    // first input check
    if (head == NULL)
        return NULL;

    Link *cur = head;  // 当前节点指针
    Link *next;        // 下一个节点指针
    Link *pre = NULL;  // 前一个节点指针

    while (cur != NULL)
    {
        if (cur->p_next == NULL)  // 到了尾节点
        {
            cur->p_next = pre;
            break;
        }
        next = cur->p_next;
        cur->p_next = pre;
        pre = cur;
        cur = next;
    }
    return cur;
}
```
测试程序代码：
``` C
int main()
{
    int arr[5] = {1,2,3,4,5};
    Link *head = createALinklist(arr, 5);
    cout << "before reverse:" << endl;
    traverseList(head);
    Link *rhead = reverseList(head);
    cout << "after reverse:" << endl;
    traverseList(rhead);

    return 0;
}
```
运行结果：
![反转链表运行结果](https://blog-pics.nos-eastchina1.126.net/result16.jpg)
当问题不好解决时，不妨多用几个指针，也许就能豁然开朗。

## 总结
这些只是单链表最基础最简单的一些操作，实际工作中的链表应用会更复杂。不过只有把最基础的东西掌握好，才能向更强的地方迈进。
还有一些如双向链表，循环链表等可能会在后续的学习中讲到。

