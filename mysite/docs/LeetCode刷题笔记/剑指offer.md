# 剑指 Offer 05. 替换空格(easy)

2023/9/13

00 : 05 : 14

---

## 题目描述

请实现一个函数，把字符串 `s` 中的每个空格替换成"%20"。

 

**示例 1：**

```
输入：s = "We are happy."
输出："We%20are%20happy."
```

 

**限制：**

```
0 <= s 的长度 <= 10000
```

----

## 我的答案

```c
class Solution {
public:
    string replaceSpace(string s) {
        int n = s.length();
        for (int i = 0; i < n; ++i)
        {
            if (s[i] == ' ')
            {
                s[i] = '%';
                s.insert(i+1, "20");
                i += 2;
                n += 2;
            }
        }
        return s;
    }
};
```

---

## 参考答案

---

## 感想

很简单, 再新建一个字符串也行, 反正都是遍历

# [剑指 Offer 06. 从尾到头打印链表](https://leetcode.cn/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)(easy)

2023/9/15

00 : 03 : 17

---

## 题目描述

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

 

**示例 1：**

```
输入：head = [1,3,2]
输出：[2,3,1]
```

 

**限制：**

```
0 <= 链表长度 <= 10000
```

----

## 我的答案

```c
class Solution {
public:
    vector<int> reversePrint(ListNode* head) {
        vector<int> res;
        ListNode *p = head;
        while (p != nullptr)
        {
            res.insert(res.begin(), p->val);
            p = p->next;
        }
        return res;
    }
};
```

---

## 参考答案

```cpp

```

---

## 感想

方法很多, 简单

# [剑指 Offer 09. 用两个栈实现队列](https://leetcode.cn/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)(easy)

2023/9/19

00 : 02 : 27

---

## 题目描述

用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 `appendTail` 和 `deleteHead` ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，`deleteHead` 操作返回 -1 )

 

**示例 1：**

```
输入：
["CQueue","appendTail","deleteHead","deleteHead","deleteHead"]
[[],[3],[],[],[]]
输出：[null,null,3,-1,-1]
```

**示例 2：**

```
输入：
["CQueue","deleteHead","appendTail","appendTail","deleteHead","deleteHead"]
[[],[],[5],[2],[],[]]
输出：[null,-1,null,null,5,2]
```

**提示：**

- `1 <= values <= 10000`
- 最多会对` appendTail、deleteHead `进行` 10000` 次调用

---

## 我的答案

```c
class CQueue {
public:
    CQueue() {

    }
    
    void appendTail(int value) {
        instk.push(value);
    }
    
    int deleteHead() {
        if (outstk.empty())
        {
            if (instk.empty())
            {
                return -1;
            }
            else
            {
                while (!instk.empty())
                {
                    int tmp = instk.top();
                    outstk.push(tmp);
                    instk.pop();
                }
            }
        }
        int tmp = outstk.top();
        outstk.pop();
        return tmp;
    }
    stack<int> instk;
    stack<int> outstk;
};
```

---

## 参考答案

```cpp
class CQueue {
private:
    stack<int> inStack, outStack;

    void in2out() {
        while (!inStack.empty()) {
            outStack.push(inStack.top());
            inStack.pop();
        }
    }

public:
    CQueue() {}

    void appendTail(int value) {
        inStack.push(value);
    }

    int deleteHead() {
        if (outStack.empty()) {
            if (inStack.empty()) {
                return -1;
            }
            in2out();
        }
        int value = outStack.top();
        outStack.pop();
        return value;
    }
};
```

---

## 感想

一个输入栈一个输出栈

# [剑指 Offer 18. 删除链表的节点](https://leetcode.cn/problems/shan-chu-lian-biao-de-jie-dian-lcof/)(easy)

2023/9/16

00 : 03 : 14

---

## 题目描述

给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。

返回删除后的链表的头节点。

**注意：**此题对比原题有改动

**示例 1:**

```
输入: head = [4,5,1,9], val = 5
输出: [4,1,9]
解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.
```

**示例 2:**

```
输入: head = [4,5,1,9], val = 1
输出: [4,5,9]
解释: 给定你链表中值为 1 的第三个节点，那么在调用了你的函数之后，该链表应变为 4 -> 5 -> 9.
```

 

**说明：**

- 题目保证链表中节点的值互不相同
- 若使用 C 或 C++ 语言，你不需要 `free` 或 `delete` 被删除的节点

----

## 我的答案

```c
class Solution {
public:
    ListNode* deleteNode(ListNode* head, int val) {
        ListNode *curr = head;
        ListNode *prev = nullptr;
        while (curr != nullptr)
        {
            if (curr->val == val)
            {
                if (prev == nullptr)
                {
                    return head->next;
                }
                else
                {
                    prev->next = curr->next;
                    return head;
                }
            }
            prev = curr;
            curr = curr->next;
        }
        return nullptr;
    }
};
```

---

## 参考答案

```cpp

```

---

## 感想

方法很多, 简单

# 剑指 Offer 20. 表示数值的字符串(medium)

2023/9/13

00 : 54 : 27

---

## 题目描述

请实现一个函数用来判断字符串是否表示**数值**（包括整数和小数）。

**数值**（按顺序）可以分成以下几个部分：

1. 若干空格
2. 一个 **小数** 或者 **整数**
3. （可选）一个 `'e'` 或 `'E'` ，后面跟着一个 **整数**
4. 若干空格

**小数**（按顺序）可以分成以下几个部分：

1. （可选）一个符号字符（`'+'` 或 `'-'`）
2. 下述格式之一：
   1. 至少一位数字，后面跟着一个点 `'.'`
   2. 至少一位数字，后面跟着一个点 `'.'` ，后面再跟着至少一位数字
   3. 一个点 `'.'` ，后面跟着至少一位数字

**整数**（按顺序）可以分成以下几个部分：

1. （可选）一个符号字符（`'+'` 或 `'-'`）
2. 至少一位数字

部分**数值**列举如下：

- `["+100", "5e2", "-123", "3.1416", "-1E-16", "0123"]`

部分**非数值**列举如下：

- `["12e", "1a3.14", "1.2.3", "+-5", "12e+5.4"]`

 

**示例 1：**

```
输入：s = "0"
输出：true
```

**示例 2：**

```
输入：s = "e"
输出：false
```

**示例 3：**

```
输入：s = "."
输出：false
```

**示例 4：**

```
输入：s = "    .1  "
输出：true
```

 

**提示：**

- `1 <= s.length <= 20`
- `s` 仅含英文字母（大写和小写），数字（`0-9`），加号 `'+'` ，减号 `'-'` ，空格 `' '` 或者点 `'.'` 。

----

## 我的答案

```c
class Solution {
public:
    bool checkInt(string s)
    {
        int n = s.length();
        if (s[0] == '+' || s[0] == '-')
        {
            if (n == 1)
                return false;
        }
        else if (s[0] < '0' || s[0] > '9')
        {
            return false;
        }
        for (int i = 1; i < n; ++i)
        {
            if (s[i] < '0' || s[i] > '9')
            {
                return false;
            }
        }
        return true;
    }
    bool checkFloat(string s, int dotIndex)
    {
        int n = s.length();
        if (dotIndex >= n)
        {
            return false;
        }
        if (dotIndex == 0 && n == 1)
        {
            return false;
        }
        int i = 0;
        if (s[i] == '+' || s[i] == '-')
        {
            i++;
        }
        for (; i < n; ++i)
        {
            if (s[i] == '.')
            {
                if (i == n-1)
                {
                    if (n == 1 || s[i-1] < '0' || s[i-1] > '9')
                    {
                        return false;
                    }
                }
                else if (s[i+1] < '0' || s[i+1] > '9' || i != dotIndex)
                {
                    return false;
                }
            }
            else if (s[i] < '0' || s[i] > '9')
            {
                return false;
            }
        }
        return true;
    }
    bool isNumber(string s) {
        int n = s.length();
        int left = 0;
        int dotIndex = -1;
        int EIndex = -1;
        while (left < n && s[left] == ' ')
        {
            ++left;
        }
        if (left == n)
        {
            return false;
        }
        int right = left;
        while (right < n && s[right] != ' ')
        {
            if (s[right] == '.')
            {
                dotIndex = right;
            }
            else if (s[right] == 'e' || s[right] == 'E')
            {
                EIndex = right;
            }
            ++right;
        }
        for (int i = right; i < n; ++i)
        {
            if (s[i] != ' ')
            {
                return false;
            }
        }
        s = s.substr(left, right-left);
        if (dotIndex != -1)
        {
           dotIndex -= left;
        }
        if (EIndex != -1)
        {
            EIndex -= left;
        }
        // 123
        if (dotIndex == -1 && EIndex == -1)
        {
            return checkInt(s);
        }
        // 1E3
        else if(dotIndex == -1)
        {
            return checkInt(s.substr(0, EIndex)) && checkInt(s.substr(EIndex+1));
        }
        // 1.23
        else if(EIndex == -1)
        {
            return checkFloat(s, dotIndex);
        }
        // 1.2E3
        else
        {
            return checkFloat(s.substr(0, EIndex), dotIndex) && checkInt(s.substr(EIndex+1));
        }
    }
};
```

---

## 参考答案

```cpp
class Solution {
public:
    enum State {
        STATE_INITIAL,
        STATE_INT_SIGN,
        STATE_INTEGER,
        STATE_POINT,
        STATE_POINT_WITHOUT_INT,
        STATE_FRACTION,
        STATE_EXP,
        STATE_EXP_SIGN,
        STATE_EXP_NUMBER,
        STATE_END
    };

    enum CharType {
        CHAR_NUMBER,
        CHAR_EXP,
        CHAR_POINT,
        CHAR_SIGN,
        CHAR_SPACE,
        CHAR_ILLEGAL
    };

    CharType toCharType(char ch) {
        if (ch >= '0' && ch <= '9') {
            return CHAR_NUMBER;
        } else if (ch == 'e' || ch == 'E') {
            return CHAR_EXP;
        } else if (ch == '.') {
            return CHAR_POINT;
        } else if (ch == '+' || ch == '-') {
            return CHAR_SIGN;
        } else if (ch == ' ') {
            return CHAR_SPACE;
        } else {
            return CHAR_ILLEGAL;
        }
    }

    bool isNumber(string s) {
        unordered_map<State, unordered_map<CharType, State>> transfer{
            {
                STATE_INITIAL, {
                    {CHAR_SPACE, STATE_INITIAL},
                    {CHAR_NUMBER, STATE_INTEGER},
                    {CHAR_POINT, STATE_POINT_WITHOUT_INT},
                    {CHAR_SIGN, STATE_INT_SIGN}
                }
            }, {
                STATE_INT_SIGN, {
                    {CHAR_NUMBER, STATE_INTEGER},
                    {CHAR_POINT, STATE_POINT_WITHOUT_INT}
                }
            }, {
                STATE_INTEGER, {
                    {CHAR_NUMBER, STATE_INTEGER},
                    {CHAR_EXP, STATE_EXP},
                    {CHAR_POINT, STATE_POINT},
                    {CHAR_SPACE, STATE_END}
                }
            }, {
                STATE_POINT, {
                    {CHAR_NUMBER, STATE_FRACTION},
                    {CHAR_EXP, STATE_EXP},
                    {CHAR_SPACE, STATE_END}
                }
            }, {
                STATE_POINT_WITHOUT_INT, {
                    {CHAR_NUMBER, STATE_FRACTION}
                }
            }, {
                STATE_FRACTION,
                {
                    {CHAR_NUMBER, STATE_FRACTION},
                    {CHAR_EXP, STATE_EXP},
                    {CHAR_SPACE, STATE_END}
                }
            }, {
                STATE_EXP,
                {
                    {CHAR_NUMBER, STATE_EXP_NUMBER},
                    {CHAR_SIGN, STATE_EXP_SIGN}
                }
            }, {
                STATE_EXP_SIGN, {
                    {CHAR_NUMBER, STATE_EXP_NUMBER}
                }
            }, {
                STATE_EXP_NUMBER, {
                    {CHAR_NUMBER, STATE_EXP_NUMBER},
                    {CHAR_SPACE, STATE_END}
                }
            }, {
                STATE_END, {
                    {CHAR_SPACE, STATE_END}
                }
            }
        };

        int len = s.length();
        State st = STATE_INITIAL;

        for (int i = 0; i < len; i++) {
            CharType typ = toCharType(s[i]);
            if (transfer[st].find(typ) == transfer[st].end()) {
                return false;
            } else {
                st = transfer[st][typ];
            }
        }
        return st == STATE_INTEGER || st == STATE_POINT || st == STATE_FRACTION || st == STATE_EXP_NUMBER || st == STATE_END;
    }
};
```

---

## 感想

这题目很麻烦, 题解的状态机的做法其实也很麻烦, 暂时没想到什么好的

# [剑指 Offer 21. 调整数组顺序使奇数位于偶数前面](https://leetcode.cn/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)(easy)

2023/9/18

00 : 03 : 16

---

## 题目描述

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数在数组的前半部分，所有偶数在数组的后半部分。

 

**示例：**

```
输入：nums = [1,2,3,4]
输出：[1,3,2,4] 
注：[3,1,2,4] 也是正确的答案之一。
```

 

**提示：**

1. `0 <= nums.length <= 50000`
2. `0 <= nums[i] <= 10000`

## 我的答案

```c
class Solution {
public:
    vector<int> exchange(vector<int>& nums) {
        int n = nums.size();
        int left = 0, right = n-1;
        while (left < right)
        {
            while (left < right && nums[left] & 1)
            {
                ++left;
            }
            while (left < right && (nums[right]+1) & 1)
            {
                --right;
            }
            swap(nums[left], nums[right]);
        }
        return nums;
    }
};
```

---

## 参考答案

```cpp
class Solution {
public:
    vector<int> exchange(vector<int>& nums) {
        int left = 0, right = nums.size() - 1;
        while (left < right) {
            while (left < right and nums[left] % 2 == 1) {
                left++;
            }
            while (left < right and nums[right] % 2 == 0) {
                right--;
            }
            if (left < right) {
                swap(nums[left++], nums[right--]);
            }
        }
        return nums;
    }
};
```

---

## 感想

快排的思想

# [剑指 Offer 22. 链表中倒数第k个节点](https://leetcode.cn/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)(easy)

2023/9/16

00 : 03 : 34

---

## 题目描述

输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。

例如，一个链表有 `6` 个节点，从头节点开始，它们的值依次是 `1、2、3、4、5、6`。这个链表的倒数第 `3` 个节点是值为 `4` 的节点。

 

**示例：**

```
给定一个链表: 1->2->3->4->5, 和 k = 2.

返回链表 4->5.
```

## 我的答案

```c
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        ListNode *left = head, *right = head;
        for (int i = 0; i < k-1; ++i)
        {
            right = right->next;
        }
        while (right->next != nullptr)
        {
            left = left->next;
            right = right->next;
        }
        return left;
    }
};
```

---

## 参考答案

```cpp

```

---

## 感想

看到标签是双指针就立马能想到这个比较巧妙的一次遍历的方法了

# [剑指 Offer 24. 反转链表](https://leetcode.cn/problems/fan-zhuan-lian-biao-lcof/)(easy)

2023/9/15

---

## 题目描述

定义一个函数，输入一个链表的头节点，反转该链表并输出反转后链表的头节点。

 

**示例:**

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

 

**限制：**

```
0 <= 节点个数 <= 5000
```

 

**注意**：本题与主站 206 题相同：https://leetcode-cn.com/problems/reverse-linked-list/

----

## 我的答案

```c
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* res = head;
        if (head == nullptr)
        {
            return nullptr;
        }
        head = head->next;
        res->next = nullptr;
        while (head != nullptr)
        {
            ListNode *tmp = res;
            res = head;
            head = head->next;
            res->next = tmp;
        }
        return res;
    }
};
```

---

## 参考答案

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr) {
            ListNode* next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
};
```

---

## 感想

简单题秒杀

# [剑指 Offer 25. 合并两个排序的链表](https://leetcode.cn/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/)(easy)

2023/9/17

00 : 05 : 28

---

## 题目描述

输入两个递增排序的链表，合并这两个链表并使新链表中的节点仍然是递增排序的。

**示例1：**

```
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

**限制：**

```
0 <= 链表长度 <= 1000
```

注意：本题与主站 21 题相同：https://leetcode-cn.com/problems/merge-two-sorted-lists/

----

## 我的答案

```c
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode *head = new ListNode(-1);
        ListNode *tail = head;
        while (l1 != nullptr && l2 != nullptr)
        {
            if (l1->val > l2->val)
            {
                tail->next = l2;
                l2 = l2->next;
            }
            else
            {
                tail->next = l1;
                l1 = l1->next;
            }
            tail = tail->next;
        }
        tail->next = l1 == nullptr ? l2 : l1;
        
        return head->next;
    }
};
```

---

## 参考答案

```cpp

```

---

## 感想

要学习哨兵的这个思想

# [剑指 Offer 30. 包含min函数的栈](https://leetcode.cn/problems/bao-han-minhan-shu-de-zhan-lcof/)(easy)

2023/9/19

00 : 12 : 51

---

## 题目描述

定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 O(1)。

 

**示例:**

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.min();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.min();   --> 返回 -2.
```

----

## 我的答案

```c
class MinStack {
public:
    /** initialize your data structure here. */
    MinStack() {
    }
    
    void push(int x) {
        if (stk.empty())
        {
            minEle = x;
        }
        stk.push(x - minEle);
        minEle = minEle < x ? minEle : x;
    }
    
    void pop() {
        if (stk.top() < 0)
        {
            minEle -= stk.top();
        }
        stk.pop();
    }
    
    int top() {
        return stk.top() < 0 ? minEle : stk.top() + minEle;
    }
    
    int min() {
        return minEle;
    }
    stack<long> stk;
    long minEle;
};
```

---

## 参考答案

```cpp
class MinStack {
public:
    /** initialize your data structure here. */
    long long num_min;
    stack<long long> stack_diff;
    MinStack() {
    }
    
    void push(int x) {
        if(stack_diff.empty())
        {
            num_min = x;
            stack_diff.push(0);
            return;
        }
        stack_diff.push((long long)x-num_min);
        if(x < num_min)
        {
            num_min = x;
        }
    }
    
    void pop() {
        if(stack_diff.top() < 0)
        {
            num_min -= stack_diff.top();
        }
        stack_diff.pop();
    }
    
    int top() {
        if(stack_diff.top() >= 0)
        {
            return stack_diff.top()+num_min;
        }
        else
        {
            return num_min;
        }
    }
    
    int min() {
        return num_min;
    }
};
```

---

## 感想

最好的做法就是不用辅助栈, 直接记录一个最小值和每个元素与最小值的差即可

# [剑指 Offer 35. 复杂链表的复制](https://leetcode.cn/problems/fu-za-lian-biao-de-fu-zhi-lcof/)(medium)

2023/9/16

00 : 09 : 30

---

## 题目描述

请实现 `copyRandomList` 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 `next` 指针指向下一个节点，还有一个 `random` 指针指向链表中的任意节点或者 `null`。

 

**示例 1：**

![img](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202309160904300.png)

```
输入：head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
输出：[[7,null],[13,0],[11,4],[10,2],[1,0]]
```

**示例 2：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/01/09/e2.png)

```
输入：head = [[1,1],[2,1]]
输出：[[1,1],[2,1]]
```

**示例 3：**

**![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/01/09/e3.png)**

```
输入：head = [[3,null],[3,0],[3,null]]
输出：[[3,null],[3,0],[3,null]]
```

**示例 4：**

```
输入：head = []
输出：[]
解释：给定的链表为空（空指针），因此返回 null。
```

 

**提示：**

- `-10000 <= Node.val <= 10000`
- `Node.random` 为空（null）或指向链表中的节点。
- 节点数目不超过 1000 。

 

**注意：**本题与主站 138 题相同：https://leetcode-cn.com/problems/copy-list-with-random-pointer/

 

## 我的答案

```c
class Solution {
public:
    Node* copyRandomList(Node* head) {
        unordered_map<Node*, Node*> Aft;
        Node *p = head;
        while (p != nullptr)
        {
            Aft[p] = new Node(p->val);
            p = p->next;
        }
        p = head;
        while (p != nullptr)
        {
            Aft[p]->next = Aft[p->next];
            Aft[p]->random = Aft[p->random];
            p = p->next;
        }
        return Aft[head];
    }
};
```

---

## 参考答案

```cpp
class Solution {
public:
    unordered_map<Node*, Node*> cachedNode;

    Node* copyRandomList(Node* head) {
        if (head == nullptr) {
            return nullptr;
        }
        if (!cachedNode.count(head)) {
            Node* headNew = new Node(head->val);
            cachedNode[head] = headNew;
            headNew->next = copyRandomList(head->next);
            headNew->random = copyRandomList(head->random);
        }
        return cachedNode[head];
    }
};
```

---

## 感想

回溯, 看起来比两遍遍历要好

# [剑指 Offer 52. 两个链表的第一个公共节点](https://leetcode.cn/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)(easy)

2023/9/17

00 : 05 : 35

---

## 题目描述

输入两个链表，找出它们的第一个公共节点。

如下面的两个链表**：**

[![img](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202309170918124.png)](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)

在节点 c1 开始相交。

 

**示例 1：**

[![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_example_1.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_1.png)

```
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
输出：Reference of the node with value = 8
输入解释：相交节点的值为 8 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
```

 

**示例 2：**

[![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_example_2.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_2.png)

```
输入：intersectVal = 2, listA = [0,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
输出：Reference of the node with value = 2
输入解释：相交节点的值为 2 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [0,9,1,2,4]，链表 B 为 [3,2,4]。在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。
```

 

**示例 3：**

[![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_example_3.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_3.png)

```
输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
输出：null
输入解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
解释：这两个链表不相交，因此返回 null。
```

 

**注意：**

- 如果两个链表没有交点，返回 `null`.
- 在返回结果后，两个链表仍须保持原有的结构。
- 可假定整个链表结构中没有循环。
- 程序尽量满足 O(*n*) 时间复杂度，且仅用 O(*1*) 内存。
- 本题与主站 160 题相同：https://leetcode-cn.com/problems/intersection-of-two-linked-lists/

----

## 我的答案

```c
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
        if (headA == nullptr || headB == nullptr)
        {
            return nullptr;
        }
        ListNode *p1 = headA, *p2 = headB, *last = nullptr;
        while (p1 != p2)
        {
            if (p1->next == nullptr)
            {
                if (last == nullptr)
                {
                    last = p1;
                }
                else if (p1 != last)
                {
                    return nullptr;
                }
                p1 = headB;
            }
            else
            {
                p1 = p1->next;
            }
            if (p2->next == nullptr)
            {
                if (last == nullptr)
                {
                    last = p2;
                }
                else if (p2 != last)
                {
                    return nullptr;
                }
                p2 = headA;
            }
            else
            {
                p2 = p2->next;
            }
        }
        return p1;
    }
};
```

---

## 参考答案

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (headA == nullptr || headB == nullptr) {
            return nullptr;
        }
        ListNode *pA = headA, *pB = headB;
        while (pA != pB) {
            pA = pA == nullptr ? headB : pA->next;
            pB = pB == nullptr ? headA : pB->next;
        }
        return pA;
    }
};
```

---

## 感想

题解更加优雅, 不相交可以理解成相交在nullptr

# [剑指 Offer 57. 和为s的两个数字](https://leetcode.cn/problems/he-wei-sde-liang-ge-shu-zi-lcof/)(easy)

2023/9/18

00 : 03 : 57

---

## 题目描述

输入一个递增排序的数组和一个数字s，在数组中查找两个数，使得它们的和正好是s。如果有多对数字的和等于s，则输出任意一对即可。

 

**示例 1：**

```
输入：nums = [2,7,11,15], target = 9
输出：[2,7] 或者 [7,2]
```

**示例 2：**

```
输入：nums = [10,26,30,31,47,60], target = 40
输出：[10,30] 或者 [30,10]
```

 

**限制：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^6`

----

## 我的答案

```c
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_set<int> visited;
        for (auto & num : nums)
        {
            if (visited.find(target - num) != visited.end())
                return vector<int>({num, target - num});
            if (visited.find(num) == visited.end())
                visited.insert({num});
        }
        return vector<int>();
    } 
};
```

---

## 参考答案

---

## 感想

没注意看是有序数组, 有序的话肯定是双指针而不是用哈希表了

# [剑指 Offer 58 - I. 翻转单词顺序](https://leetcode.cn/problems/fan-zhuan-dan-ci-shun-xu-lcof/)(easy)

2023/9/18

00 : 12 : 52

---

## 题目描述

输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student. "，则输出"student. a am I"。

 

**示例 1：**

```
输入: "the sky is blue"
输出: "blue is sky the"
```

**示例 2：**

```
输入: "  hello world!  "
输出: "world! hello"
解释: 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
```

**示例 3：**

```
输入: "a good   example"
输出: "example good a"
解释: 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。
```

 

**说明：**

- 无空格字符构成一个单词。
- 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
- 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。

**注意：**本题与主站 151 题相同：https://leetcode-cn.com/problems/reverse-words-in-a-string/

**注意：**此题对比原题有改动

----

## 我的答案

```c
class Solution {
public:
    string reverseWords(string s) {
        int n = s.length();
        int left = 0, right = 0;
        string res;
        while (left < n && s[left] == ' ')
            left++;
        right = left;
        while (right < n && s[right] != ' ')
            right++;
        res = s.substr(left, right - left);
        left = ++right;

        while (left < n)
        {
            while (left < n && s[left] == ' ')
                left++;
            right = left;
            while (right < n && s[right] != ' ')
                right++;
            if (right > left)
                res = s.substr(left, right - left) + " " + res;
            left = ++right;
        }
        return res;
    }
};
```

---

## 参考答案

```cpp
class Solution {
public:
    string reverseWords(string s) {
        int left = 0, right = s.size() - 1;
        // 去掉字符串开头的空白字符
        while (left <= right && s[left] == ' ') ++left;

        // 去掉字符串末尾的空白字符
        while (left <= right && s[right] == ' ') --right;

        deque<string> d;
        string word;

        while (left <= right) {
            char c = s[left];
            if (word.size() && c == ' ') {
                // 将单词 push 到队列的头部
                d.push_front(move(word));
                word = "";
            }
            else if (c != ' ') {
                word += c;
            }
            ++left;
        }
        d.push_front(move(word));
        
        string ans;
        while (!d.empty()) {
            ans += d.front();
            d.pop_front();
            if (!d.empty()) ans += ' ';
        }
        return ans;
    }
};
```

---

## 感想

也可以先整个反转, 在局部反转

# 剑指 Offer 58 - II. 左旋转字符串(easy)

2023/9/13

00 : 01 : 26

---

## 题目描述

字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。

 

**示例 1：**

```
输入: s = "abcdefg", k = 2
输出: "cdefgab"
```

**示例 2：**

```
输入: s = "lrloseumgh", k = 6
输出: "umghlrlose"
```

 

**限制：**

- `1 <= k < s.length <= 10000`

----

## 我的答案

```c
class Solution {
public:
    string reverseLeftWords(string s, int n) {
        string res = s.substr(n);
        res += s.substr(0, n);
        return res;
    }
};
```

---

## 参考答案

---

## 感想

简单

# [剑指 Offer 59 - I. 滑动窗口的最大值](https://leetcode.cn/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)(hard)

2023/9/20

00 : 19 : 58

---

## 题目描述

给定一个数组 `nums` 和滑动窗口的大小 `k`，请找出所有滑动窗口里的最大值。

**示例:**

```
输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
输出: [3,3,5,5,6,7] 
解释: 

  滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

 

**提示：**

你可以假设 *k* 总是有效的，在输入数组 **不为空** 的情况下，`1 ≤ k ≤ nums.length`。

注意：本题与主站 239 题相同：https://leetcode-cn.com/problems/sliding-window-maximum/

----

## 我的答案

```c
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        deque<int> q;
        int n = nums.size();
        vector<int> res;
        for (int i = 0; i < k; ++i)
        {
            while (!q.empty() && nums[i] >= nums[q.back()])
            {
                q.pop_back();
            }
            q.push_back(i);
        }
        res.push_back(nums[q.front()]);
        for (int i = k; i < n; ++i)
        {
            while (!q.empty() && nums[i] >= nums[q.back()])
            {
                q.pop_back();
            }
            q.push_back(i);
            while (q.front() <= i - k)
            {
                q.pop_front();
            }
            res.push_back(nums[q.front()]);
        }
        return res;
    }
};
```

---

## 参考答案

```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        int n = nums.size();
        deque<int> q;
        for (int i = 0; i < k; ++i) {
            while (!q.empty() && nums[i] >= nums[q.back()]) {
                q.pop_back();
            }
            q.push_back(i);
        }

        vector<int> ans = {nums[q.front()]};
        for (int i = k; i < n; ++i) {
            while (!q.empty() && nums[i] >= nums[q.back()]) {
                q.pop_back();
            }
            q.push_back(i);
            while (q.front() <= i - k) {
                q.pop_front();
            }
            ans.push_back(nums[q.front()]);
        }
        return ans;
    }
};
```

---

## 感想

做过, 但是只记得用堆了, 印象中还有更好的算法, 看了眼笔记知道是单调队列再写的

# [剑指 Offer 59 - II. 队列的最大值](https://leetcode.cn/problems/dui-lie-de-zui-da-zhi-lcof/)(medium)

2023/9/20

00 : 18 : 21

---

## 题目描述

请定义一个队列并实现函数 `max_value` 得到队列里的最大值，要求函数`max_value`、`push_back` 和 `pop_front` 的**均摊**时间复杂度都是O(1)。

若队列为空，`pop_front` 和 `max_value` 需要返回 -1

**示例 1：**

```
输入: 
["MaxQueue","push_back","push_back","max_value","pop_front","max_value"]
[[],[1],[2],[],[],[]]
输出: [null,null,null,2,1,2]
```

**示例 2：**

```
输入: 
["MaxQueue","pop_front","max_value"]
[[],[],[]]
输出: [null,-1,-1]
```

 

**限制：**

- `1 <= push_back,pop_front,max_value的总操作数 <= 10000`
- `1 <= value <= 10^5`

----

## 我的答案

```c
class MaxQueue {
public:
    MaxQueue() {

    }
    
    int max_value() {
        if (assist.empty()) return -1;
        return assist.front();
    }
    
    void push_back(int value) {
        q.push(value);
        while (!assist.empty() && value > assist.back())
        {
            assist.pop_back();
        }
        assist.push_back(value);
    }
    
    int pop_front() {
        if (assist.empty()) return -1;
        int tmp = q.front();
        q.pop();
        if (tmp == assist.front())
        {
            assist.pop_front();
        }
        return tmp;
    }
    deque<int> assist;
    queue<int> q;
};
```

---

## 参考答案

```cpp
class MaxQueue {
    queue<int> q;
    deque<int> d;
public:
    MaxQueue() {
    }
    
    int max_value() {
        if (d.empty())
            return -1;
        return d.front();
    }
    
    void push_back(int value) {
        while (!d.empty() && d.back() < value) {
            d.pop_back();
        }
        d.push_back(value);
        q.push(value);
    }
    
    int pop_front() {
        if (q.empty())
            return -1;
        int ans = q.front();
        if (ans == d.front()) {
            d.pop_front();
        }
        q.pop();
        return ans;
    }
};
```

---

## 感想

和上一题是连着写的, 正好再应用了一次单调队列

# 剑指 Offer 67. 把字符串转换成整数(medium)

2023/9/14

00 : 20 : 48

---

## 题目描述

写一个函数 StrToInt，实现把字符串转换成整数这个功能。不能使用 atoi 或者其他类似的库函数。

 

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。

当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。

该字符串除了有效的整数部分之后也可能会存在多余的字符，这些字符可以被忽略，它们对于函数不应该造成影响。

注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换。

在任何情况下，若函数不能进行有效的转换时，请返回 0。

**说明：**

假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [−231, 231 − 1]。如果数值超过这个范围，请返回  INT_MAX (231 − 1) 或 INT_MIN (−231) 。

**示例 1:**

```
输入: "42"
输出: 42
```

**示例 2:**

```
输入: "   -42"
输出: -42
解释: 第一个非空白字符为 '-', 它是一个负号。
     我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。
```

**示例 3:**

```
输入: "4193 with words"
输出: 4193
解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。
```

**示例 4:**

```
输入: "words and 987"
输出: 0
解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
     因此无法执行有效的转换。
```

**示例 5:**

```
输入: "-91283472332"
输出: -2147483648
解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
     因此返回 INT_MIN (−231) 。
```

 

注意：本题与主站 8 题相同：https://leetcode-cn.com/problems/string-to-integer-atoi/

----

## 我的答案

```c
class Solution {
public:
    int strToInt(string str) {
        int n = str.length();
        int i = 0, sign = 1;
        unsigned long max = 2147483647, res = 0;
        while (str[i] == ' ')
        {
            i++;
        }
        str = str.substr(i);
        n = str.size();
        if (str[0] <= '9' && str[0] >= '0')
        {
            res += str[0] - '0';
        }
        else if (str[0] == '-' || str[0] == '+')
        {
            sign = str[0] == '-' ? -1 : 1;
            max = sign == 1 ? max : max + 1;
        }
        else 
        {
            return 0;
        }
        if (n == 1 || str[1] < '0' || str[1] > '9') return res * sign;
        else
        {
            res = 10 * res + str[1] - '0';
        }
        for (i = 2; i < n; ++i)
        {
            if (str[i] >= '0' && str[i] <= '9')
            {
                if(10 * res + str[i] - '0' > max)
                {
                    return max * sign;
                }
                else
                {
                    res = 10 * res + str[i] - '0'; 
                }
            }
            else 
            {
                break;
            }
        }
        return res * sign;
    }
};
```

---

## 参考答案

```cpp
class Automaton {
    string state = "start";
    unordered_map<string, vector<string>> table = {
        {"start", {"start", "signed", "in_number", "end"}},
        {"signed", {"end", "end", "in_number", "end"}},
        {"in_number", {"end", "end", "in_number", "end"}},
        {"end", {"end", "end", "end", "end"}}
    };

    int get_col(char c) {
        if (isspace(c)) return 0;
        if (c == '+' or c == '-') return 1;
        if (isdigit(c)) return 2;
        return 3;
    }
public:
    int sign = 1;
    long long ans = 0;

    void get(char c) {
        state = table[state][get_col(c)];
        if (state == "in_number") {
            ans = ans * 10 + c - '0';
            ans = sign == 1 ? min(ans, (long long)INT_MAX) : min(ans, -(long long)INT_MIN);
        }
        else if (state == "signed")
            sign = c == '+' ? 1 : -1;
    }
};

class Solution {
public:
    int strToInt(string str) {
        Automaton automaton;
        for (char c : str)
            automaton.get(c);
        return automaton.sign * automaton.ans;
    }
};
```

---

## 感想

又是状态机, 我不喜欢, 可能是我水平还没到吧

# [LCR 120. 寻找文件副本](https://leetcode.cn/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)(easy)

2023/9/22

00 : 01: 56

---

## 题目描述

设备中存有 `n` 个文件，文件 `id` 记于数组 `documents`。若文件 `id` 相同，则定义为该文件存在副本。请返回任一存在副本的文件 `id`。

 

**示例 1：**

```
输入：documents = [2, 5, 3, 0, 5, 0]
输出：0 或 5
```

 

**提示：**

- `0 ≤ documents[i] ≤ n-1`
- `2 <= n <= 100000`

 

----

## 我的答案

```c
class Solution {
public:
    int findRepeatDocument(vector<int>& documents) {
        int n = documents.size();
        for (int i = 0; i < n; ++i)
        {
            if (i == documents[i])
            {
                continue;
            }
            else if (documents[documents[i]] == documents[i])
            {
                return documents[i];
            }
            else
            {
                swap(documents[i], documents[documents[i]]);
                i--;
            }
        }
        return -1;
    }
};
```

---

## 参考答案

```cpp
class Solution {
public:
    int findRepeatNumber(vector<int>& nums) {
        int i = 0;
        while(i < nums.size()) {
            if(nums[i] == i) {
                i++;
                continue;
            }
            if(nums[nums[i]] == nums[i])
                return nums[i];
            swap(nums[i],nums[nums[i]]);
        }
        return -1;
    }
};
```

---

## 感想

最容易想到的方法是哈希表, 用原地交换的这个方法可以省内存

# [LCR 146. 螺旋遍历二维数组](https://leetcode.cn/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/)(easy)

2023/9/21

00 : 36 : 48

---

## 题目描述

给定一个二维数组 `array`，请返回「**螺旋遍历**」该数组的结果。

**螺旋遍历**：从左上角开始，按照 **向右**、**向下**、**向左**、**向上** 的顺序 **依次** 提取元素，然后再进入内部一层重复相同的步骤，直到提取完所有元素。

 

**示例 1：**

```
输入：array = [[1,2,3],[8,9,4],[7,6,5]]
输出：[1,2,3,4,5,6,7,8,9]
```

**示例 2：**

```
输入：array  = [[1,2,3,4],[12,13,14,5],[11,16,15,6],[10,9,8,7]]
输出：[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16]
```

 

**限制：**

- `0 <= array.length <= 100`
- `0 <= array[i].length <= 100`

注意：本题与主站 54 题相同：https://leetcode-cn.com/problems/spiral-matrix/

----

## 我的答案

```c
class Solution {
public:
    int m, n;
    void next(pair<int, int>& p, vector<vector<bool>> &passedby)
    {
        int x = p.first;
        int y = p.second;
        passedby[x+1][y+1] = true;
        if (passedby[x][y+1] && passedby[x+1][y] && !passedby[x+1][y+2])
        {
            p.second++;
            return;
        }
        if (passedby[x+1][y+2] && passedby[x][y+1] && !passedby[x+2][y+1])
        {
            p.first++;
            return;
        }
        if (passedby[x+1][y+2] && passedby[x+2][y+1] && !passedby[x+1][y])
        {
            p.second--;
            return;
        }
        if (passedby[x+2][y+1] && passedby[x+1][y] && !passedby[x][y+1])
        {
            p.first--;
            return;
        }
    }
    vector<int> spiralArray(vector<vector<int>>& matrix) {
        m = matrix.size();
        if (m == 0)
        {
            return vector<int>(); 
        }
        n = matrix[0].size();
        if (m == 0 || n == 0)
        {
            return vector<int>();
        }
        vector<vector<bool>> passedby(m+2, vector<bool>(n+2, false));
        pair<int, int> p;
        vector<int> ans(m * n);
        for (int i = 0; i <= m; ++i)
        {
            passedby[i][0] = true;
            passedby[i][n+1] = true;
        }
        for (int i = 0; i <= n; ++i)
        {
            passedby[0][i] = true;
            passedby[m+1][i] = true;
        }
        for (int i = 0; i < m * n; ++i)
        {
            ans[i] = matrix[p.first][p.second];
            next(p, passedby);
        }
        return ans;
    }
};
```

---

## 参考答案

```cpp
class Solution {
private:
    static constexpr int directions[4][2] = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
public:
    vector<int> spiralArray(vector<vector<int>>& array) {
        if (array.size() == 0 || array[0].size() == 0) {
            return {};
        }
        
        int rows = array.size(), columns = array[0].size();
        vector<vector<bool>> visited(rows, vector<bool>(columns));
        int total = rows * columns;
        vector<int> order(total);

        int row = 0, column = 0;
        int directionIndex = 0;
        for (int i = 0; i < total; i++) {
            order[i] = array[row][column];
            visited[row][column] = true;
            int nextRow = row + directions[directionIndex][0], nextColumn = column + directions[directionIndex][1];
            if (nextRow < 0 || nextRow >= rows || nextColumn < 0 || nextColumn >= columns || visited[nextRow][nextColumn]) {
                directionIndex = (directionIndex + 1) % 4;
            }
            row += directions[directionIndex][0];
            column += directions[directionIndex][1];
        }
        return order;
    }
};
```

---

## 感想

题目理解起来是不难, 但是代码写起来很恶心, 感觉应该是medium难度比较合适

# [LCR 148. 验证图书取出顺序](https://leetcode.cn/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/)(medium)

2023/9/21

00 : 08 : 28

---

## 题目描述

现在图书馆有一堆图书需要放入书架，并且图书馆的书架是一种特殊的数据结构，只能按照 **一定** 的顺序 **放入** 和 **拿取** 书籍。

给定一个表示图书放入顺序的整数序列 `putIn`，请判断序列 `takeOut` 是否为按照正确的顺序拿取书籍的操作序列。你可以假设放入书架的所有书籍编号都不相同。

 

**示例 1：**

```
输入：putIn = [6,7,8,9,10,11], takeOut = [9,11,10,8,7,6]
输出：true
解释：我们可以按以下操作放入并拿取书籍：
push(6), push(7), push(8), push(9), pop() -> 9,
push(11), push(10),pop() -> 11,pop() -> 10, pop() -> 8, pop() -> 7, pop() -> 6
```

**示例 2：**

```
输入：putIn = [6,7,8,9,10,11], takeOut = [11,9,8,10,6,7]
输出：false
解释：6 不能在 7 之前取出。
```

 

**提示：**

- `0 <= putIn.length == takeOut.length <= 1000`
- `0 <= putIn[i], takeOut < 1000`
- `putIn` 是 `takeOut` 的排列。

注意：本题与主站 946 题相同：https://leetcode-cn.com/problems/validate-stack-sequences/

----

## 我的答案

```c
class Solution {
public:
    bool validateBookSequences(vector<int>& putIn, vector<int>& takeOut) {
        stack<int> stk;
        int n = putIn.size();
        int remain = n;
        for (int i = 0; i < n; ++i)
        {
            if (putIn[i] != takeOut[n - remain])
            {
                stk.push(putIn[i]);
            }
            else
            {
                remain--;
                while (!stk.empty() && stk.top() == takeOut[n - remain])
                {
                    remain--;
                    stk.pop();
                }
            }
        }
        return remain == 0;
    }
};
```

---

## 参考答案

```cpp
class Solution {
public:
    bool validateBookSequences(vector<int>& putIn, vector<int>& takeOut) {
        stack<int> stk;
        int n = putIn.size();
        int remain = n;
        for (int i = 0; i < n; ++i)
        {
            stk.push(putIn[i]);
            while (!stk.empty() && stk.top() == takeOut[n - remain])
            {
                remain--;
                stk.pop();
            }
        }
        return remain == 0;
    }
};
```

---

## 感想

这题就比较简单了, 应该为easy

# [LCR 172. 统计目标成绩的出现次数](https://leetcode.cn/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)(easy)

2023/9/22

00 : 01 : 06

---

## 题目描述

某班级考试成绩按非严格递增顺序记录于整数数组 `scores`，请返回目标成绩 `target` 的出现次数。

 

**示例 1：**

```
输入: scores = [2, 2, 3, 4, 4, 4, 5, 6, 6, 8], target = 4
输出: 3
```

**示例 2：**

```
输入: scores = [1, 2, 3, 5, 7, 9], target = 6
输出: 0
```

 

**提示：**

- `0 <= scores.length <= 105`
- `-109 <= scores[i] <= 109`
- `scores` 是一个非递减数组
- `-109 <= target <= 109`

 

**注意：**本题与主站 34 题相同（仅返回值不同）：https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/

 

----

## 我的答案

```c
class Solution {
public:
    int binarySearchLeft(vector<int>& scores, int &target, int left, int right)
    {
        if (left == right)
        {
            return scores[left] == target ? left : -1;
        }
        int mid = (left + right) / 2;
        if (scores[mid] < target)
        {
            return binarySearchLeft(scores, target, mid + 1, right);
        }
        else
        {
            return binarySearchLeft(scores, target, left, mid);
        }
    }
    int binarySearchRight(vector<int>& scores, int &target, int left, int right)
    {
        if (left == right)
        {
            return scores[left] == target ? left : -1;
        }
        int mid = (left + right + 1) / 2;
        if (scores[mid] <= target)
        {
            return binarySearchRight(scores, target, mid, right);
        }
        else
        {
            return binarySearchRight(scores, target, left, mid -1);
        }
    }
    int countTarget(vector<int>& scores, int target) {
        if (scores.size() == 0)
        {
            return 0;
        }
        int left = binarySearchLeft(scores, target, 0, scores.size()-1);
        int right = binarySearchRight(scores, target, 0, scores.size()-1);
        return right != -1 ? right - left + 1 : 0;
    }
};
```

---

## 参考答案

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        return upper_bound(begin(nums), end(nums), target) - lower_bound(begin(nums), end(nums), target);
    }
};
```

---

## 感想

一开始没有看到是递增的序列, 直接O(n)遍历了, 还在想怎么这么无脑, 看了题解才发现是递增序列, 又再写了一遍二分的版本, 不得不说二分的边界确实不好判断
