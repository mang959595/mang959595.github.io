---
title: 暑期算法学习day1
date: 2022-07-31 00:45:08
tags: 算法
categoreies: labuladong算法学习
---

# 暑假算法学习day1

<br>

## day1——核心框架汇总

### 1-框架思维

#### 基本数据结构及其操作

数据结构的存储方式只有两种：**数组（顺序存储）和链表（链式存储）**

在此之上可以构成：**散列表、栈、队列、堆、树、图等等各种数据结构**

基本操作：无非就是**遍历 + 访问**，再具体一点就是：**增删查改**。

数据结构种类很多，但它们存在的目的都是在不同的应用场景，尽可能高效地增删查改。

---

#### 算法的框架思维

数组遍历框架

```c++

voidtraverse(int[]arr){

    for(int i =0; i <arr.length; i++){

        // 迭代访问 arr[i]

    }

}

```

<br>

链表遍历框架，兼具迭代和递归结构：

```c++

/* 基本的单链表节点 */

classListNode{

    int val;

    ListNode next;

}


voidtraverse(ListNodehead){

    for(ListNode p = head; p != null; p =p.next){

        // 迭代访问 p.val

    }

}


voidtraverse(ListNodehead){

    // 递归访问 head.val

    traverse(head.next);

}

```

<br>

二叉树遍历框架，典型的非线性递归遍历结构：

```c++

/* 基本的二叉树节点 */

classTreeNode{

    int val;

    TreeNode left, right;

}


voidtraverse(TreeNoderoot){

    traverse(root.left);

    traverse(root.right);

}

```

<br>

N 叉树的遍历框架：

```cpp

/* 基本的 N 叉树节点 */

classTreeNode{

    int val;

    TreeNode[] children;

}


voidtraverse(TreeNoderoot){

    for(TreeNode child :root.children)

        traverse(child);

}

```

**1、先学习像数组、链表这种基本数据结构的常用算法**

**2、学会基础算法之后，不要急着上来就刷回溯算法、动态规划这类笔试常考题，而应该先刷二叉树**

---

<br>

### 2-刷题心得

#### 数组/单链表系列算法

> -**单链表常考的技巧就是双指针**

> -**数组常用的技巧有很大一部分还是双指针相关的技巧，说白了是教你如何聪明地进行穷举**

> -**滑动窗口算法技巧，典型的快慢双指针，快慢指针中间就是滑动的「窗口」，主要用于解决子串问题。**

> -**最后说说 [前缀和技巧](https://labuladong.github.io/algo/2/20/24/) 和 [差分数组技巧](https://labuladong.github.io/algo/2/20/25/)**。

>> 如果频繁地让你计算子数组的和，每次用 for 循环去遍历肯定没问题，但前缀和技巧预计算一个 `preSum` 数组，就可以避免循环。
>>

>> 类似的，如果频繁地让你对子数组进行增减操作，也可以每次用 for 循环去操作，但差分数组技巧维护一个 `diff` 数组，也可以避免循环。
>>

<br>

#### 二叉树系列算法

二叉树题目的递归解法可以分两类思路，第一类是遍历一遍二叉树得出答案，第二类是通过分解问题计算出答案，这两类思路分别对应着 [回溯算法核心框架](https://labuladong.github.io/algo/4/31/105/) 和 [动态规划核心框架](https://labuladong.github.io/algo/3/25/69/)。

更进一步，图论相关的算法也是二叉树算法的延续。

比如 [图论基础](https://labuladong.github.io/algo/2/22/50/)， [环判断和拓扑排序](https://labuladong.github.io/algo/2/22/51/) 和 [二分图判定算法](https://labuladong.github.io/algo/2/22/52/) 就用到了 DFS 算法；再比如 [Dijkstra 算法模板](https://labuladong.github.io/algo/2/22/56/)，就是改造版 BFS 算法加上一个类似 dp table 的数组。

这些算法的本质都是穷举二（多）叉树，有机会的话通过剪枝或者备忘录的方式减少冗余计算，提高效率，就这么点事儿。

---

<br>

### 3-双指针（七道链表题）

<br>

#### 合并两个有序链表

用到了3个指针：指向原链表的p1、p2，连接链表的p

注意要新建一个空的链表头

```cpp

ListNodemergeTwoLists(ListNodel1,ListNodel2){

    // 虚拟头结点

    ListNode dummy =newListNode(-1), p = dummy;

    ListNode p1 = l1, p2 = l2;

  

    while(p1 != null && p2 != null){

        // 比较 p1 和 p2 两个指针

        // 将值较小的的节点接到 p 指针

        if(p1.val>p2.val){

            p.next= p2;

            p2 =p2.next;

        }else{

            p.next= p1;

            p1 =p1.next;

        }

        // p 指针不断前进

        p =p.next;

    }

  

    if(p1 != null){

        p.next= p1;

    }

  

    if(p2 != null){

        p.next= p2;

    }

  

    returndummy.next;

}

```

<br>

#### 链表的分解

![img](https://labuladong.github.io/algo/images/%e9%93%be%e8%a1%a8%e6%8a%80%e5%b7%a7/title4.jpg)

<br>

#### 合并k个有序链表

利用优先级队列（堆）

```c++

ListNodemergeKLists(ListNode[]lists){

    if(lists.length==0)return null;

    // 虚拟头结点

    ListNode dummy =newListNode(-1);

    ListNode p = dummy;

    // 优先级队列，最小堆

    PriorityQueue<ListNode> pq =newPriorityQueue<>(

        lists.length,(a, b)->(a.val-b.val));

    // 将 k 个链表的头结点加入最小堆

    for(ListNode head : lists){

        if(head != null)

            pq.add(head);

    }


    while(!pq.isEmpty()){

        // 获取最小节点，接到结果链表中

        ListNode node =pq.poll();

        p.next= node;

        if(node.next!= null){

            pq.add(node.next);

        }

        // p 指针不断前进

        p =p.next;

    }

    returndummy.next;

}

```

优先队列 `pq` 中的元素个数最多是 `k`，所以一次 `poll` 或者 `add` 方法的时间复杂度是 `O(logk)`；所有的链表节点都会被加入和弹出 `pq`，**所以算法整体的时间复杂度是 `O(Nlogk)`，其中 `k` 是链表的条数，`N` 是这些链表的节点总数**。

<br>

#### 寻找单链表的倒数第k个节点

不告诉链表长度；

只遍历一次链表的解法：用p1走k步后，p2再出发

```cpp

// 返回链表的倒数第 k 个节点

ListNodefindFromEnd(ListNodehead,intk){

    ListNode p1 = head;

    // p1 先走 k 步

    for(int i =0; i < k; i++){

        p1 =p1.next;

    }

    ListNode p2 = head;

    // p1 和 p2 同时走 n - k 步

    while(p1 != null){

        p2 =p2.next;

        p1 =p1.next;

    }

    // p2 现在指向第 n - k + 1 个节点，即倒数第 k 个节点

    return p2;

}

```

<br>

#### 寻找单链表的中点

利用快慢指针：快指针走两步，慢指针走一步

```cpp

ListNodemiddleNode(ListNodehead){

    // 快慢指针初始化指向 head

    ListNode slow = head, fast = head;

    // 快指针走到末尾时停止

    while(fast != null &&fast.next!= null){

        // 慢指针走一步，快指针走两步

        slow =slow.next;

        fast =fast.next.next;

    }

    // 慢指针指向中点

    return slow;

}

```

需要注意的是，如果链表长度为偶数，也就是说中点有两个的时候，我们这个解法返回的节点是靠后的那个节点。

<br>

#### 判断单链表是否包含环并找出环起点

判断是否含环：快慢指针，如果最终快指针赶上慢指针说明有环。否则快指针遍历直到空指针。

```cpp

booleanhasCycle(ListNodehead){

    // 快慢指针初始化指向 head

    ListNode slow = head, fast = head;

    // 快指针走到末尾时停止

    while(fast != null &&fast.next!= null){

        // 慢指针走一步，快指针走两步

        slow =slow.next;

        fast =fast.next.next;

        // 快慢指针相遇，说明含有环

        if(slow == fast){

            returntrue;

        }

    }

    // 不包含环

    returnfalse;

}

```

若有环，如何判断环的起点

> 当快慢指针相遇时，让其中任一个指针指向头节点，然后让它俩以相同速度前进，再次相遇时所在的节点位置就是环开始的位置。

```cpp

ListNodedetectCycle(ListNodehead){

    ListNode fast, slow;

    fast = slow = head;

    while(fast != null &&fast.next!= null){

        fast =fast.next.next;

        slow =slow.next;

        if(fast == slow)break;

    }

    // 上面的代码类似 hasCycle 函数

    if(fast == null ||fast.next== null){

        // fast 遇到空指针说明没有环

        return null;

    }


    // 重新指向头结点

    slow = head;

    // 快慢指针同步前进，相交点就是环起点

    while(slow != fast){

        fast =fast.next;

        slow =slow.next;

    }

    return slow;

}

```

<br>

#### 判断两个单链表是否相交并找出交点

不使用Hashmap的实现，即仅使用两个指针

> 我们可以让 `p1` 遍历完链表 `A` 之后开始遍历链表 `B`，让 `p2` 遍历完链表 `B` 之后开始遍历链表 `A`，这样相当于「逻辑上」两条链表接在了一起。

```cpp

ListNodegetIntersectionNode(ListNodeheadA,ListNodeheadB){

    // p1 指向 A 链表头结点，p2 指向 B 链表头结点

    ListNode p1 = headA, p2 = headB;

    while(p1 != p2){

        // p1 走一步，如果走到 A 链表末尾，转到 B 链表

        if(p1 == null) p1 = headB;

        else            p1 =p1.next;

        // p2 走一步，如果走到 B 链表末尾，转到 A 链表

        if(p2 == null) p2 = headA;

        else            p2 =p2.next;

    }

    return p1;

}

```

> 或者通过预先求出两个链表的长度，来使p1、p2同时到达相交节点

```cpp

public ListNode getIntersectionNode(ListNode headA, ListNode headB){

    int lenA =0, lenB =0;

    // 计算两条链表的长度

    for(ListNode p1 = headA; p1 != null; p1 =p1.next){

        lenA++;

    }

    for(ListNode p2 = headB; p2 != null; p2 =p2.next){

        lenB++;

    }

    // 让 p1 和 p2 到达尾部的距离相同

    ListNode p1 = headA, p2 = headB;

    if(lenA > lenB){

        for(int i =0; i < lenA - lenB; i++){

            p1 =p1.next;

        }

    }else{

        for(int i =0; i < lenB - lenA; i++){

            p2 =p2.next;

        }

    }

    // 看两个指针是否会相同，p1 == p2 时有两种情况：

    // 1、要么是两条链表不相交，他俩同时走到尾部空指针

    // 2、要么是两条链表相交，他俩走到两条链表的相交点

    while(p1 != p2){

        p1 =p1.next;

        p2 =p2.next;

    }

    return p1;

}

```

---

<br>

### 4-双指针（七道数组题）

> 链表和数组题中的双指针有两种

> -**左右指针**，就是两个指针相向而行或者相背而行；

> -**快慢指针**，就是两个指针同向而行，一快一慢。

<br>

#### 快慢指针技巧

> 原地修改数组

##### 删除有序数组中的重复项

在不新开数组的情况下的解法

![img](https://labuladong.github.io/algo/images/%e6%95%b0%e7%bb%84%e5%8e%bb%e9%87%8d/1.gif)

```cpp

intremoveDuplicates(int[]nums){

    if(nums.length==0){

        return0;

    }

    int slow =0, fast =0;

    while(fast <nums.length){

        if(nums[fast]!=nums[slow]){

            slow++;

            // 维护 nums[0..slow] 无重复

            nums[slow]=nums[fast];

        }

        fast++;

    }

    // 数组长度为索引 + 1

    return slow +1;

}

```

对于删除有序链表中的重复项呢？

![img](https://labuladong.github.io/algo/images/%e6%95%b0%e7%bb%84%e5%8e%bb%e9%87%8d/2.gif)

```cpp

ListNodedeleteDuplicates(ListNodehead){

    if(head == null)return null;

    ListNode slow = head, fast = head;

    while(fast != null){

        if(fast.val!=slow.val){

            // nums[slow] = nums[fast];

            slow.next= fast;

            // slow++;

            slow =slow.next;

        }

        // fast++

        fast =fast.next;

    }

    // 断开与后面重复元素的连接

    slow.next= null;

    return head;

}

```

<br>

##### 删除无序数组中的某个元素

```cpp

intremoveElement(int[]nums,intval){

    int fast =0, slow =0;

    while(fast <nums.length){

        if(nums[fast]!= val){

            nums[slow]=nums[fast];

            slow++;

        }

        fast++;

    }

    return slow;

}

```

这里是先给 `nums[slow]` 赋值然后再给 slow++

<br>

##### 移动0

> 比如说给你输入 `nums = [0,1,4,0,2]`，你的算法没有返回值，但是会把 `nums` 数组原地修改成 `[1,4,2,0,0]`。

> 其实就相当于移除 `nums` 中的所有 0，然后再把后面的元素都赋值为 0 即可。

可以复用上一题的 `removeElement` 函数

<br>

#### 滑动窗口类型（快慢指针）

代码框架

```cpp

/* 滑动窗口算法框架 */

voidslidingWindow(strings,stringt){

    unordered_map<char,int> need, window;

    for(char c : t)need[c]++;

  

    int left =0, right =0;

    int valid =0;

    while(right <s.size()){

        char c =s[right];

        // 右移（增大）窗口

        right++;

        // 进行窗口内数据的一系列更新


        while(window needs shrink){

            char d =s[left];

            // 左移（缩小）窗口

            left++;

            // 进行窗口内数据的一系列更新

        }

    }

}

```

`left` 指针在后，`right` 指针在前，两个指针中间的部分就是「窗口」，算法通过扩大和缩小「窗口」来解决某些问题。

---

<br>

#### 左右指针技巧

##### 二分查找

简单框架

```cpp

intbinarySearch(int[]nums,inttarget){

    // 一左一右两个指针相向而行

    int left =0, right =nums.length-1;

    while(left <= right){

        int mid =(right + left)/2;

        if(nums[mid]== target)

            return mid;

        elseif(nums[mid]< target)

            left = mid +1;

        elseif(nums[mid]> target)

            right = mid -1;

    }

    return-1;

}

```

<br>

##### 两数之和

返回的下标是从1算起的

```cpp

int[] twoSum(int[] nums,int target){

    // 一左一右两个指针相向而行

    int left =0, right =nums.length-1;

    while(left < right){

        int sum =nums[left]+nums[right];

        if(sum == target){

            // 题目要求的索引是从 1 开始的

            returnnewint[]{left +1, right +1};

        }elseif(sum < target){

            left++; // 让 sum 大一点 *

        }elseif(sum > target){

            right--; // 让 sum 小一点 *

        }

    }

    returnnewint[]{-1,-1};

}

```

<br>

##### 反转数组

```cpp

voidreverseString(char[]s){

    // 一左一右两个指针相向而行

    int left =0, right =s.length-1;

    while(left < right){

        // 交换 s[left] 和 s[right]

        char temp =s[left];

        s[left]=s[right];

        s[right]= temp;

        left++;

        right--;

    }

}

```

<br>

##### 回文串匹配

简单匹配

```cpp

booleanisPalindrome(Strings){

    // 一左一右两个指针相向而行

    int left =0, right =s.length()-1;

    while(left < right){

        if(s.charAt(left)!=s.charAt(right)){

            returnfalse;

        }

        left++;

        right--;

    }

    returntrue;

}

```

<br>

**最长回文子串**

> 左右指针从中间往两边延申

> 如果回文串的长度为奇数，参数 r = l

> 如果回文串的长度为偶数，参数 r = l+1

> ```cpp 
> // 在 s 中寻找以 s[l] 和 s[r] 为中心的最长回文串
> Stringpalindrome(Strings,intl,intr){
> // 防止索引越界
> while(l >=0&& r <s.length()
> &&s.charAt(l)==s.charAt(r)){
> // 双指针，向两边展开
> l--; r++;
> }
> // 返回以 s[l] 和 s[r] 为中心的最长回文串
> returns.substring(l +1, r);
> }
> ```

```cpp

StringlongestPalindrome(Strings){

    String res ="";

    for(int i =0; i <s.length(); i++){

        // 以 s[i] 为中心的最长回文子串

        String s1 =palindrome(s, i, i);

        // 以 s[i] 和 s[i+1] 为中心的最长回文子串

        String s2 =palindrome(s, i, i +1);

        // res = longest(res, s1, s2)

        res =res.length()>s1.length()? res : s1;

        res =res.length()>s2.length()? res : s2;

    }

    return res;

}

```
