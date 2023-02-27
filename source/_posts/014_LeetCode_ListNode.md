---
title: LeetCode 链表
description: leetcode刷题
date: 2023-2-24
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/24_10_58_22_image-20230224105818700.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/24_10_58_22_image-20230224105818700.png
categories: 
- Leetcode
tag: 
- Leetcode
- 算法
- c++

---

 

#  合并两个有序链表

> https://leetcode.cn/problems/merge-two-sorted-lists/description/

**示例 1：**

![image-20230224105818700](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/24_10_58_22_image-20230224105818700.png)

```
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

**示例 2：**

```
输入：l1 = [], l2 = []
输出：[]
```

**示例 3：**

```
输入：l1 = [], l2 = [0]
输出：[0]
```

**提示：**

- 两个链表的节点数目范围是 `[0, 50]`
- `-100 <= Node.val <= 100`
- `l1` 和 `l2` 均按 **非递减顺序** 排列

**解法一：双指针 + 循环**

创建一个新的链表`prehead`，使用三个指针分别指在`prehead list1 list2`三个链表的头部，通过循环比较将较小值的连在链表`prehead`的后面。循环遍历之后，当有一个链表已经指向空时，将不为空的链表直接连在链表`prehead`之后。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        ListNode* prehead = new ListNode(-1);
        ListNode* prev = prehead;
        while(list1 != nullptr && list2 != nullptr){
            if(list1->val <list2->val){
                prev->next = list1;
                list1= list1->next;
                
            }else{
                prev->next = list2;
                list2 = list2->next;
            }
            prev= prev->next;

        }
        prev->next = list1 == nullptr ?list2:list1;
        return prehead->next;
    }
};
```

**解法2：递归**

首先需要考虑的时`list1 list2`是否为空链表作为边界条件，如果都不为空，进行判断`list1 list2`的值的大小进行接下来递归的大小链表的判定。

```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        if (list1 == nullptr) {
            return list2;
        } else if (list2 == nullptr) {
            return list1;
        } else if (list1->val < list2->val) {
            list1->next = mergeTwoLists(list1->next, list2);
            return list1;
        } else {
            list2->next = mergeTwoLists(list1, list2->next);
            return list2;
        }

    }
};
```

#  删除排序链表中的重复元素

> https://leetcode.cn/problems/remove-duplicates-from-sorted-list/description/

给定一个已排序的链表的头 `head` ， *删除所有重复的元素，使每个元素只出现一次* 。返回 *已排序的链表* 。

**示例 1：**

![image-20230224112342204](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/24_11_23_44_image-20230224112342204.png)

```
输入：head = [1,1,2]
输出：[1,2]
```

**示例 2：**

![image-20230224112354722](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/24_11_23_57_image-20230224112354722.png)

```
输入：head = [1,1,2,3,3]
输出：[1,2,3]
```

**提示：**

- 链表中节点数目在范围 `[0, 300]` 内
- `-100 <= Node.val <= 100`
- 题目数据保证链表已经按升序 **排列**

**解法：一次遍历**

根据提示里可知，链表顺序时排列好的，重复数据的位置是连续的。所以只需要判断前一个和后一个位置的值是否相同。如果相同则将前一个位置的指针指向后一个指针所指的位置。

> 两个指针版本

```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if(head == nullptr){
            return head;
        }
        ListNode* pre = head;
        ListNode* back = head->next;
        while(back != nullptr){
            if(pre->val == back->val){
                back = back->next;
                pre->next = back ;
            }else{
                pre = pre->next;
                back = back->next;
            }
        }
        return head;
    }
};
```

> 一个指针版本

```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head) {
            return head;
        }

        ListNode* pre = head;
        while (pre->next) {
            if (pre->val == pre->next->val) {
                pre->next = pre->next->next;
            }
            else {
                pre = pre->next;
            }
        }

        return head;
    }
};
```

# 环形链表

> https://leetcode.cn/problems/linked-list-cycle/description/

给你一个链表的头节点 `head` ，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。**注意：`pos` 不作为参数进行传递** 。仅仅是为了标识链表的实际情况。

*如果链表中存在环* ，则返回 `true` 。 否则，返回 `false` 。



**示例 1：**

![image-20230225110507408](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/25_11_5_9_image-20230225110507408.png)

```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

**示例 2：**

![image-20230225110518032](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/25_11_5_20_image-20230225110518032.png)

```
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```

**示例 3：**

![image-20230225110527687](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/25_11_5_29_image-20230225110527687.png)

```
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```

 

**提示：**

- 链表中节点的数目范围是 `[0, 104]`
- `-105 <= Node.val <= 105`
- `pos` 为 `-1` 或者链表中的一个 **有效索引** 。

**解法：双指针**

> 贴一个总结数组和链表许多经典问题的题解解析
>
> https://leetcode.cn/problems/linked-list-cycle/solutions/175734/yi-wen-gao-ding-chang-jian-de-lian-biao-wen-ti-h-2/

针对本题，我们采用快慢两种指针的方式去查询链表是否有环，快指针一次走两步，慢指针一次走一步。如果有环的情况下，快指针总是会追得上慢指针，当快指针和慢指针指向同一个位置时，说明链表有环。

```c++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode *slow = head;
        ListNode *fast = head;
        while(fast != nullptr) {
            fast = fast->next;
            if(fast != nullptr) {
                fast = fast->next;
            }
            if(fast == slow) {
                return true;
            }
            slow = slow->next;
        }
        return false;
    };
};

```

 #  环形链表 II

> https://leetcode.cn/problems/linked-list-cycle-ii/description/

给定一个链表的头节点  `head` ，返回链表开始入环的第一个节点。 *如果链表无环，则返回 `null`。*

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（**索引从 0 开始**）。如果 `pos` 是 `-1`，则在该链表中没有环。**注意：`pos` 不作为参数进行传递**，仅仅是为了标识链表的实际情况。

**不允许修改** 链表。



**示例 1：**

![image-20230226104339393](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/26_10_43_42_image-20230226104339393.png)

```
输入：head = [3,2,0,-4], pos = 1
输出：返回索引为 1 的链表节点
解释：链表中有一个环，其尾部连接到第二个节点。
```

**示例 2：**

![image-20230226104357772](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/26_10_43_59_image-20230226104357772.png)

```
输入：head = [1,2], pos = 0
输出：返回索引为 0 的链表节点
解释：链表中有一个环，其尾部连接到第一个节点。
```

**示例 3：**

![image-20230226104413534](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/26_10_44_15_image-20230226104413534.png)

```
输入：head = [1], pos = -1
输出：返回 null
解释：链表中没有环。
```

 

**提示：**

- 链表中节点的数目范围在范围 `[0, 104]` 内
- `-105 <= Node.val <= 105`
- `pos` 的值为 `-1` 或者链表中的一个有效索引

**解法一：双指针**

> 一个带有图的讲解链接：https://leetcode.cn/problems/linked-list-cycle-ii/solutions/12616/linked-list-cycle-ii-kuai-man-zhi-zhen-shuang-zhi-/

通过环形链表一，使用双指针可以判断出是否存在环，并且条件成立时快慢指针指在相同的位置。

1. 设链表共有 `a+b` 个节点，从链表头部到环入口为 `a` 个节点，链表环有 `b` 个节点。
2. 分别设快慢指针走了`f` 和 `s` 步，可得 `f = 2s` 。
3. `f` 指针比 `s` 指针多走了 `n` 个环的长度，得 `f = s + nb`
4. 由第二步以及第三步可得，`f = 2nb , s = nb`。
5. 如果让一个指针从链表头部一直走，走到链表环入口处，需要的步数 `k`  为 `k = a + nb` 。
6. 由第四步可知，当快慢指针相遇时，`s` 已经走了 `nb` 步，只需要再走 `a` 步便可以到达环入口。此时让快指针从链表头部开始走，当它第一次走到环入口时所需要的步数也为 `a` 步，此时两个指针指向同一个位置，便是环入口。

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode *slow = head, *fast = head;
        while (fast != nullptr) {
            slow = slow->next;
            if (fast->next == nullptr) {
                return nullptr;
            }
            fast = fast->next->next;
            if (fast == slow) {
                ListNode *ptr = head;
                while (ptr != slow) {
                    ptr = ptr->next;
                    slow = slow->next;
                }
                return ptr;
            }
        }
        return nullptr;
    }
};
```

**解法二：哈希表**

我们遍历链表中的每个节点，并将它记录下来；一旦遇到了此前遍历过的节点，就可以判定链表中存在环。借助哈希表可以很方便地实现。

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        unordered_set<ListNode *> visited;
        while (head != nullptr) {
            if (visited.count(head)) {
                return head;
            }
            visited.insert(head);
            head = head->next;
        }
        return nullptr;
    }
};
```

# 相交链表

> https://leetcode.cn/problems/intersection-of-two-linked-lists/description/

给你两个单链表的头节点 `headA` 和 `headB` ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 `null` 。

图示两个链表在节点 `c1` 开始相交**：**

![image-20230226113925362](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/26_11_39_28_image-20230226113925362.png)

题目数据 **保证** 整个链式结构中不存在环。

**注意**，函数返回结果后，链表必须 **保持其原始结构** 。

**自定义评测：**

**评测系统** 的输入如下（你设计的程序 **不适用** 此输入）：

- `intersectVal` - 相交的起始节点的值。如果不存在相交节点，这一值为 `0`
- `listA` - 第一个链表
- `listB` - 第二个链表
- `skipA` - 在 `listA` 中（从头节点开始）跳到交叉节点的节点数
- `skipB` - 在 `listB` 中（从头节点开始）跳到交叉节点的节点数

评测系统将根据这些输入创建链式数据结构，并将两个头节点 `headA` 和 `headB` 传递给你的程序。如果程序能够正确返回相交节点，那么你的解决方案将被 **视作正确答案** 。

 

**示例 1：**

![image-20230226113948185](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/26_11_39_50_image-20230226113948185.png)

```
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,6,1,8,4,5], skipA = 2, skipB = 3
输出：Intersected at '8'
解释：相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。
从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,6,1,8,4,5]。
在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
— 请注意相交节点的值不为 1，因为在链表 A 和链表 B 之中值为 1 的节点 (A 中第二个节点和 B 中第三个节点) 是不同的节点。换句话说，它们在内存中指向两个不同的位置，而链表 A 和链表 B 中值为 8 的节点 (A 中第三个节点，B 中第四个节点) 在内存中指向相同的位置。
```

 

**示例 2：**

![image-20230226114000910](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/26_11_40_2_image-20230226114000910.png)

```
输入：intersectVal = 2, listA = [1,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
输出：Intersected at '2'
解释：相交节点的值为 2 （注意，如果两个链表相交则不能为 0）。
从各自的表头开始算起，链表 A 为 [1,9,1,2,4]，链表 B 为 [3,2,4]。
在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。
```

**示例 3：**

![image-20230226114010933](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/26_11_40_12_image-20230226114010933.png)

```
输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
输出：null
解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。
由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
这两个链表不相交，因此返回 null 。
```

 

**提示：**

- `listA` 中节点数目为 `m`
- `listB` 中节点数目为 `n`
- `1 <= m, n <= 3 * 104`
- `1 <= Node.val <= 105`
- `0 <= skipA <= m`
- `0 <= skipB <= n`
- 如果 `listA` 和 `listB` 没有交点，`intersectVal` 为 `0`
- 如果 `listA` 和 `listB` 有交点，`intersectVal == listA[skipA] == listB[skipB]`



**解法：双指针**

让两个链表从同距离末尾同等距离的位置开始遍历。这个位置只能是较短链表的头结点位置。 为此，我们必须消除两个链表的长度差

指针 pA 指向 A 链表，指针 pB 指向 B 链表，依次往后遍历
如果 pA 到了末尾，则 pA = headB 继续遍历
如果 pB 到了末尾，则 pB = headA 继续遍历
比较长的链表指针指向较短链表head时，长度差就消除了
如此，只需要将最短链表遍历两次即可找到位置

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if(headA == nullptr || headB == nullptr){
            return NULL;
        }
        ListNode* pa = headA;
        ListNode* pb = headB;
        //当pa pb 同时指向null时 说明同时指到链表末尾，没有相交
        while(pa != pb){
            pa = pa == nullptr ? headB : pa->next;
            pb = pb == nullptr ? headA : pb->next;
        }
        return pa;
    }
};
```

# 回文链表

> https://leetcode.cn/problems/palindrome-linked-list/description/

给你一个单链表的头节点 `head` ，请你判断该链表是否为回文链表。如果是，返回 `true` ；否则，返回 `false` 。

 

**示例 1：**

![image-20230227102811872](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/27_10_28_17_image-20230227102811872.png)

```
输入：head = [1,2,2,1]
输出：true
```

**示例 2：**

![image-20230227102825720](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/02/27_10_28_27_image-20230227102825720.png)

```
输入：head = [1,2]
输出：false
```

 

**提示：**

- 链表中节点数目在范围`[1, 105]` 内
- `0 <= Node.val <= 9`

 

**进阶：**你能否用 `O(n)` 时间复杂度和 `O(1)` 空间复杂度解决此题？

**解法：快慢指针 + 反转链表**

通过快慢指针我们可以找到链表的（slow）中间位置和（fast）最后位置。如果链表长度为偶数的话，`fast` 指针指向 `nullptr`，`slow` 指针指向中间位置；如果链表长度为奇数的话，`fast` 指针指向链表最后一位，`slow` 指针指向中心位置。以 `slow` 指针为界，拆分为两部分，奇数链表的后一部分会比前一部分多一位。反转 `slow` 指针指向的后一部分链表，将其与原链表比较，可得是否为回文链表。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        ListNode* fast = head, *slow = head;
        while(fast != nullptr && fast->next !=nullptr){
            fast = fast->next->next;
            slow = slow->next;
        }

        fast = head;
        slow = reverse(slow);

        while(slow != nullptr){
            if(slow->val != fast->val)
                return false;
            slow = slow->next;
            fast = fast->next;
        }
        return true;
    };
    ListNode* reverse(ListNode* head){
        ListNode* prev = head;
        ListNode* end = nullptr;
        while(prev){
            prev = head->next;
            head->next = end;
            end = head;
            head = prev;
        }
        return end;
    }
};
```



# 链表中倒数第k个节点

输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。

例如，一个链表有 `6` 个节点，从头节点开始，它们的值依次是 `1、2、3、4、5、6`。这个链表的倒数第 `3` 个节点是值为 `4` 的节点。

 

**示例：**

```
给定一个链表: 1->2->3->4->5, 和 k = 2.

返回链表 4->5.
```

**解法：快慢指针**

让快指针先移动 `k` 步，然后同时移动快慢指针，每次一步，当快指针为 `nullptr` 时，说明到达了链表最后，此时 `slow` 的位置便是链表倒数第 `k` 个位置。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        ListNode* fast = head, *slow = head;
        while(k>0){
            k--;
            fast = fast->next;
        }
        while(fast != nullptr){
            slow = slow->next;
            fast = fast->next;
        }
        return slow;
    }
};
```

