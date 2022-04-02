# 1 基础数据结构

## 1.1 数组/链表

### 1.1.0 杂题

- O(1)实现插入、删除、随机抓阄

  - leecode：380

  ```c++
  // 存储元素的值
  vector<int> nums;
  // 记录每个元素对应在 nums 中的索引
  unordered_map<int,int> valToIndex;
  
  删除的时候，将target与末尾元素交换位置，再删除（利用了元素无序性）
  ```

- O(1)随机抓阄，但不能在黑名单里

  - leecode：710

  - 同上，规定[0,N-blacklist.size())里的元素是有效的。于是需要把所有黑名单数移到末尾。

### 1.1.1 差分数组/前缀和

- 前缀和

  - 主要适用：原始数组**不被修改**的情况下，频繁查询**某个区间**的**累加和**。

    ![图片](labuladong.assets/640.webp)

    ```c++
    class PrefixSum {
        // 前缀和数组
        private int[] prefix;
    
        /* 输入一个数组，构造前缀和 */
        public PrefixSum(int[] nums) {
            prefix = new int[nums.length + 1];
            // 计算 nums 的累加和
            for (int i = 1; i < prefix.length; i++) {
                prefix[i] = prefix[i - 1] + nums[i - 1];
            }
        }
    
        /* 查询闭区间 [i, j] 的累加和 */
        public int query(int i, int j) {
            return prefix[j + 1] - prefix[i];
        }
    }
    ```

    

- 差分数组

  - 主要适用：频繁对原始数组的**某个区间**的元素进行**增减**。最后，问数组各个元素是多少。

  - 如果想对区间nums[i..j]的元素全部加 3，那么只需要让diff[i] += 3，然后再让diff[j+1] -= 3即可：

    ![图片](labuladong.assets/640-16450160636312.webp)

    ```c++
    class Difference {
        // 差分数组
        private int[] diff;
    
        public Difference(int[] nums) {
            assert nums.length > 0;
            diff = new int[nums.length];
            // 构造差分数组
            diff[0] = nums[0];
            for (int i = 1; i < nums.length; i++) {
                diff[i] = nums[i] - nums[i - 1];
            }
        }
    
        /* 给闭区间 [i,j] 增加 val（可以是负数）*/
        public void increment(int i, int j, int val) {
            diff[i] += val;
            if (j + 1 < diff.length) {
                diff[j + 1] -= val;
            }
        }
    
        public int[] result() {
            int[] res = new int[diff.length];
            // 根据差分数组构造结果数组
            res[0] = diff[0];
            for (int i = 1; i < diff.length; i++) {
                res[i] = res[i - 1] + diff[i];
            }
            return res;
        }
    }
    ```

  - leecode:1109

### 1.1.2 滑动窗口

- 处理各种字符串**子串问题**

  - 基本板子

    ```c++
    /* 滑动窗口算法框架 */
    // s是source，t是target
    void slidingWindow(string s, string t) {
        unordered_map<char, int> need, window;
        for (char c : t) need[c]++;
    
        int left = 0, right = 0;
        int valid = 0; 
        while (right < s.size()) {
            // c 是将移入窗口的字符
            char c = s[right];
            // 右移窗口
            right++;
            // 进行窗口内数据的一系列更新
            ...
    
            /*** debug 输出的位置 ***/
            printf("window: [%d, %d)\n", left, right);
            /********************/
    
            // 判断左侧窗口是否要收缩
            while (window needs shrink) {
                // d 是将移出窗口的字符
                char d = s[left];
                // 左移窗口
                left++;
                // 进行窗口内数据的一系列更新
                ...
            }
        }
    }
    ```

  - 需要注意的细节

    ```c++
    /*顺序问题*/
    
    // 右移时，以下两者等价（左移同理）
    char c = s[right]; //本来没进来的，这次进入
    right++;
    
    right++; // 先移动，但因为左闭右开，所以需要r-1
    char c = s[right-1];
    ```

    ```c++
    /*终止条件*/
    
    // r<s.size()，可以每次都让r右移
    // 最后一次进while是r=n-1，r进去后取n。由于是[l,r)所以正好
     while (right < s.size()) {}
    ```

    ```c++
    /*一些细节*/
    
    //先看这次进入的字符是不是需要的
    if(need.count(c)){...}
    ```

- 最小覆盖子串

  - leecode：76

- 字符串排列

  - leecode：567

- 最长无重复子串

  - leecode：3

### 1.1.3 二分查找（边界）

- 查找**左边界**时，返回的是“**比target小的个数**”

  - > `nums = [1,1,7,7,13], target = 7`，返回 2
    >
    > `nums = [2,3,5,7], target = 8`，返回 4
    >
    > `nums = [2,3,5,7]`,`target = 1`，返回 0

  - 板子

  ```c++
  // 查找左边界时
  int left_bound(int[] nums, int target) {
      if (nums.length == 0) return -1;
      int left = 0;
      // 查找范围为[left,right)
      int right = nums.length; // 注意
  	// 当出现[2,2)时，[2,2)内已经没有元素了，退出条件为left==right
      while (left < right) { // 注意
          int mid = (left + right) / 2;
          if (nums[mid] == target) {  // 目前的命中，去[left,mid)继续找
              right = mid;
          } else if (nums[mid] < target) {  // 目前的小了，去[mid+1,right)继续找
              left = mid + 1;
          } else if (nums[mid] > target) {  // 目前的大了，去[left,mid)继续找
              right = mid; // 注意
          }
      }
      // 如果nums存在target，那left指向最左的target
      // 如果nums不存在target，那left指向第一个比target大的数（有可能==nums.size)
      return left;
  }
  ```

- 同理，**右边界**

  - 右边界的return是**left-1**(right-1)，与左边界不同
    - 究其根本，是在[left,right)模式下，nums[mid]==target时，左边界与右边界的处理策略不同。左边界时，是[left,mid)；右边界时，是[mid+1,right)。正是因为右边界需要+1，才使得return需要left-1

  ```c++
  int right_bound(int[] nums, int target) {
      if (nums.length == 0) return -1;
      int left = 0;
      // 查找范围为[left,right)
      int right = nums.length; // 注意
  	// 当出现[2,2)时，[2,2)内已经没有元素了，退出条件为left==right
      while (left < right) { // 注意
          int mid = (left + right) / 2;
          if (nums[mid] == target) {  // 目前的命中，去[mid+1,right)继续找
              left = mid + 1;
          } else if (nums[mid] < target) {  // 目前的小了，去[mid+1,right)继续找
              left = mid + 1;
          } else if (nums[mid] > target) {  // 目前的大了，去[left,mid)继续找
              right = mid; // 注意
          }
      }
      // 如果nums存在target，那left-1指向最右的target
      // 如果nums不存在target，那left-1指向第一个比target大的数（所以有可能==nums.size)
      return left-1;
  }
  ```

- 当nums中不存在target时，不会进入nums[mid] == target的情况。左边界与右边界行为一致，都指向**第一个比target大的数**

### 1.1.4 双指针技巧

#### 1.1.4.1 快慢指针

- 一般慢指针一次走一步，快指针一次走两步

- 判断链表**是否有环**

  - 快与慢相遇则有环

    ```c++
    boolean hasCycle(ListNode head) {
        ListNode fast, slow;
        fast = slow = head;
        // 差一步走到头 && 走到头了 都需要特判
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
    
            if (fast == slow) return true;
        }
        return false;
    }
    ```

- 已知链表中含有环，返回这个环的**起始位置**

  - 相遇一次后，让两个指针**都变为一次一步**，再走一次，相遇处即是环起点

    - 原因：设环长度为c、一开始slow走k步，则fast走2k步。设第一次相遇处距离环起始m步。则head至环起点有k-m步。考察fast比slow多走的k步，一定是c的整数倍（slow一定会在一圈内被fast追上，于是fast除去直线与环的小尾巴：2k-((k-m)+m)=k，k是纯走环的距离）。于是让slow从head走k-m步时达到环起点，而此时，fast在环内从m处走了k-m步，也到达环起点。

    ```c++
    ListNode detectCycle(ListNode head) {
        ListNode fast, slow;
        fast = slow = head;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) break;
        }
        // 上面的代码类似 hasCycle 函数
        slow = head;
        while (slow != fast) {
            // fast一次走一步
            fast = fast.next;
            slow = slow.next;
        }
        return slow;
    }
    ```

- 寻找**环中点**

  - 当链表的长度是**奇数**时，`slow`恰巧停在**中点位置**；如果长度是**偶数**，`slow`最终的位置是**中间偏右**

    ```c++
    ListNode middleNode(ListNode head) {
        ListNode fast, slow;
        fast = slow = head;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }
        // slow 就在中间位置
        return slow;
    }
    ```

- 删除**倒数第n个**元素

  - 快指针先走`n`步，然后快慢指针开始同速前进

    ```c++
    ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode fast, slow;
        fast = slow = head;
        // 快指针先前进 n 步
        while (n-- > 0) {
            fast = fast.next;
        }
        if (fast == null) {
            // 如果此时快指针走到头了，
            // 说明倒数第 n 个节点就是第一个节点
            return head.next;
        }
        // 让慢指针和快指针同步向前
        while (fast != null && fast.next != null) {
            fast = fast.next;
            slow = slow.next;
        }
        // slow.next 就是倒数第 n 个节点，删除它
        slow.next = slow.next.next;
        return head;
    }
    ```

#### 1.1.4.2 左右指针

目前看，左右指针一般应用于**有序数组**

- 二分查找

- 两数之和

  - 给定升序排列的数组，找出两个数，求和等于target

    - 暴力需要$O(n^2)$，用左右指针只需要$O(n)$
    - 最初，小的最小，大的最大。如果此时sum<target，一定怪罪于小的太小了（大的都已经最大了）；同理，sum>target，是因为大的太大了。以sum>target为例，于是，开始把大的向左移，直到sum<target为止。此时，问题就出现在小的上了，把小的向右移，以此类推。

    ```c++
    int[] twoSum(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int sum = nums[left] + nums[right];
            if (sum == target) {
                // 题目要求的索引是从 1 开始的
                return new int[]{left + 1, right + 1};
            } else if (sum < target) {
                left++; // 让 sum 大一点
            } else if (sum > target) {
                right--; // 让 sum 小一点
            }
        }
        return new int[]{-1, -1};
    }
    ```

- 反转数组

- 滑动窗口

- 田忌赛马

  - leecode:870
  - 为排序好的设置left和right，每派出去一个，就向中间移动一次


#### 1.1.4.3 O(1)空间问题

- 数组去重、移除特定元素
  - leecode：26、27
  - slow指针在后面等着，每当fast找到一个ans里应该存在的数，就放进slow里，slow++

### 1.1.5 链表合并

- 合并2个有序链表

  - leecode：21

  - 共设置四个指针

    - ans，ans->next是最后返回的答案（虚拟头结点）
    - head，此时“ans链”的头结点，即：下一个结点放入head.next
    - p1、p2，遍历两个链

    <img src="labuladong.assets/9B5C156A4ABEF4BDA538C0B80BB0FCFB.png" alt="img" style="zoom: 33%;" />

    <img src="labuladong.assets/CB1D43D770843E0E1AAE037324140A04.png" alt="img" style="zoom:33%;" />

  - 有可能p1先结束了，之后需要两个while确保p1和p2都结束

- 合并k个有序链表

  - 大致与2个相同，不过需要使用一个priority_queue，比较每次的k个元素

### 1.1.6  链表反转

- 将一个链表反转（顺序）

  - 代码

    ```c++
    ListNode* reverseList(ListNode* head) {
        ListNode* pre=nullptr,cur=head;
    	if(head==nullptr) return head;
    	while(cur!=nullptr){
    		tmp=cur->next;
    		cur->next=pre;
    		pre=cur;
    		cur=tmp;
    	}
    	return pre;
    }
    ```

    <img src="labuladong.assets/1599866-20200321093159197-1449279238.png" alt="img" style="zoom:50%;" />

- 将一个链表反转（递归）

  - 代码

    ```c++
    ListNode reverse(ListNode head) {
        if (head.next == null) return head;
        ListNode last = reverse(head.next);
        head.next.next = head;
        head.next = null;
        return last;
    }
    ```

  - 思路

    - **对于递归算法，最重要的就是明确递归函数的定义**。具体来说，我们的`reverse`函数定义是这样的：

      **输入一个节点`head`，将「以`head`为起点」的链表反转，并返回反转之后的头结点**。

    - 明白了函数的定义，再来看这个问题。比如说我们想反转这个链表：

      <img src="labuladong.assets/image-20220303220856851.png" alt="image-20220303220856851" style="zoom:33%;" />

    - 那么输入`reverse(head)`后，会在这里进行递归：

      ```
      ListNode last = reverse(head.next);
      ```

    - 不要跳进递归（你的脑袋能压几个栈呀？），而是要根据刚才的函数定义，来弄清楚这段代码会产生什么结果：

      <img src="labuladong.assets/image-20220303220927258.png" alt="image-20220303220927258" style="zoom:33%;" />

    - 按照定义，这个`reverse(head.next)`执行完成后，整个链表应该变成了这样：

      <img src="labuladong.assets/image-20220303220944768.png" alt="image-20220303220944768" style="zoom:33%;" />

    - 并且根据函数定义，`reverse`函数会返回反转之后的头结点，我们用变量`last`接收了。

      现在再来看下面的代码：

      ```
      head.next.next = head;
      ```

      <img src="labuladong.assets/image-20220303221102340.png" alt="image-20220303221102340" style="zoom:33%;" />

    - 接下来进行的操作：

      ```
      head.next = null;
      return last;
      ```

      <img src="labuladong.assets/image-20220303221126123.png" alt="image-20220303221126123" style="zoom:33%;" />

    - 神不神奇，这样整个链表就反转过来了！递归代码就是这么简洁优雅，不过其中有两个地方需要注意：

      **1、递归函数要有 base case**，也就是这句：

      ```
      if (head.next == null) return head;
      ```

      意思是如果链表只有一个节点的时候反转也是它自己，直接返回即可。

      **2、当链表递归反转之后，新的头节点是`last`，而之前的`head`变成了最后一个节点，别忘了链表的末尾要指向 null：**

      ```
      head.next = null;
      ```

      理解了这两点后，我们就可以进一步深入了，接下来的问题其实都是在这个算法上的扩展。

- 反转前N个结点

  - 同理，直接把reverseN当做已经实现完成去考虑。已知reverseN返回的是“反转之后的头结点”。

    ```c++
    ListNode reverseN(ListNode head, int n) {
        // base case
        {……}
        // 以 head.next 为起点，需要反转前 n - 1 个节点
        ListNode last = reverseN(head.next, n - 1);
    	// 反转处理
        {……}
        return last;
    }
    ```

  - 此时，链表变成了这样

    <img src="labuladong.assets/20F4ADDCB99AA2E13AA0F077DFC5B55A.png" alt="img" style="zoom: 33%;" />

  - 于是，只需要把2指向1,1指向4，返回3即可

    ```c++
    ListNode reverseN(ListNode head, int n) {
        // base case
        {……}
        // 以 head.next 为起点，需要反转前 n - 1 个节点
        ListNode last = reverseN(head.next, n - 1);
    	// 反转处理
        ListNode tmp = head.next;
        head.next = head.next.next;
        tmp.next = head;
        
        return last;
    }
    ```

  - 最后，解决base case问题

    ```c++
    ListNode reverseN(ListNode head, int n) {
        // base case
        if(n==1){
            return head;
        }
        // 以 head.next 为起点，需要反转前 n - 1 个节点
        ListNode last = reverseN(head.next, n - 1);
    	// 反转处理
        ListNode tmp = head.next;
        head.next = head.next.next;
        tmp.next = head;
        
        return last;
    }
    ```

- 反转[m,n]个节点

  - 只需要改变代码中的if(n==1)即可

## 1.2 队列/栈

### 1.2.1 括号匹配

- 多种括号正确性检验

  - 判断准则

    ```
    Input: "()[]{}"
    Output: true
    
    Input: "([)]"
    Output: false
    
    Input: "{[]}"
    Output: true
    ```

  - 代码

    - 思路：1.每次遇到一种右括号时，去看栈里的左括号是否与他匹配——不匹配就false 2.最后看左括号是否用完了。

    ```c++
    bool isValid(string str) {
        stack<char> left;
        for (char c : str) {
            if (c == '(' || c == '{' || c == '[')
                left.push(c);
            else { // 字符 c 是右括号
                if (!left.empty() && leftOf(c) == left.top())
                    left.pop();
                else
                    // 和最近的左括号不匹配
                    return false;
            }
        }
        // 是否所有的左括号都被匹配了
        return left.empty();
    }
    
    char leftOf(char c) {
        if (c == '}') return '{';
        if (c == ')') return '(';
        return '[';
    }
    ```

- 平衡括号串（1对1）

  - 输入一个字符串`s`，可以在其中的任意位置插入 左括号`(` 或者 右括号`) `，问最少需要几次插入才能使得`s`变成一个合法的括号串

  - 代码

    - leecode：921
    - 需要注意，遇到 “)” 后，对need--，此时如果need为负数，说明“此时左括号已经用完了，这个右括号是多的”。所以需要把need置回0，另外用res记录每个“多出来的右括号”

    ```c++
    int minAddToMakeValid(string s) {
        // res 记录插入次数
        int res = 0;
        // need 变量记录右括号的需求量
        int need = 0;
    
        for (int i = 0; i < s.size(); i++) {
            if (s[i] == '(') {
                // 对右括号的需求 + 1
                need++;
            }
    
            if (s[i] == ')') {
                // 对右括号的需求 - 1
                need--;
    
                if (need == -1) {
                    need = 0;
                    // 需插入一个左括号
                    res++;
                }
            }
        }
    
        return res + need;
    }
    ```

- 平衡括号串（1对2）

  - 1个 “（” 匹配2个 “）”

  - 有两点与上面不同

    - 当need=0，但出现了“）”
      - ex：`()))`。此时，一定需要插入一个"("，变成`())  ()`，并且之后需要再出现一个")"
    - 当need为奇数，但出现了"("
      - ex：`()(`。此时，一定需要插入一个")"，变成`()) (`，随之带来的是，对")"的需求就会少一个（第一个左括号想要两个，但都到了第二个左括号了，只出现了一个。那就只能告诉第一个左括号，你别再等那一个了，我直接插入给你）。

  - 代码

    - leecode:1541

    ```c++
    int minInsertions(string s) {
        //res：确认要插入的")"与"("，need：后面希望出现的")"
        int res = 0, need = 0;
    
        for (int i = 0; i < s.size(); i++) {
            if (s[i] == '(') {
                need += 2;
                if (need % 2 == 1) {
                    res++;
                    need--;
                }
            }
            if (s[i] == ')') {
                need--;
                if (need == -1) {
                    res++;
                    need = 1;
                }
            }
        }
        return res + need;
    }
    ```

### 1.2.2 单调栈

- 单调栈模板

  - 给你一个数组，返回一个等长的数组，对应索引存储着下一个更大元素，如果没有更大的元素，就存 -1。比如说，输入一个数组`nums = [2,1,2,4,3]`，你返回数组`[4,2,4,-1,-1]`。

  - 代码

    - 单调栈指的是，维护的那个栈，每个状态都是单调的。

    ```c++
    vector<int> nextGreaterElement(vector<int>& nums) {
        vector<int> res(nums.size()); // 存放答案的数组
        stack<int> s;
        // 倒着往栈里放
        for (int i = nums.size() - 1; i >= 0; i--) {
            // 判定个子高矮
            while (!s.empty() && s.top() <= nums[i]) {
                // 矮个起开，反正也被挡着了。。。
                s.pop();
            }
            // nums[i] 身后的 next great number
            res[i] = s.empty() ? -1 : s.top();
            // 
            s.push(nums[i]);
        }
        return res;
    }
    ```

- 单调栈+问索引差距

  - 给你一个数组`T`，这个数组存放的是近几天的天气气温，你返回一个等长的数组，计算：**对于每一天，你还要至少等多少天才能等到一个更暖和的气温；如果等不到那一天，填 0**。比如说给你输入`T = [73,74,75,71,69,76]`，你返回`[1,1,3,2,1,0]`。
  - 这里只需要让单调栈里**存数组索引**，每次去数组里取值即可

### 1.2.3 单调队列

- 返回一个滑动的定长窗口中的max

  - “如果我进来时，发现有人比我小，那么你一定没用 “

    - 原因：1. 你比我先进来，于是要先出去 2. 有我在的时候，max一定不是你。

  - 代码

    - 需要注意，初始只能填充k-1个。如果填充了k个，那for之外还需要再添加一次。

    ```c++
    class MonotonicQueue {
    private:
        deque<int> data;
    public:
        void push(int n) {
            while (!data.empty() && data.back() < n) 
                data.pop_back();
            data.push_back(n);
        }
        
        int max() { return data.front(); }
        
        void pop(int n) {
            if (!data.empty() && data.front() == n)
                data.pop_front();
        }
    };
    
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        MonotonicQueue window;
        vector<int> res;
        for (int i = 0; i < nums.size(); i++) {
            if (i < k - 1) { //先填满窗口的前 k - 1
                window.push(nums[i]);
            } else { // 窗口向前滑动
                window.push(nums[i]);
                res.push_back(window.max());
                window.pop(nums[i - k + 1]);
            }
        }
        return res;
    }
    ```

### 1.2.4 去除重复字母

- 要求一、**要去重**。

  要求二、去重字符串中的字符顺序**不能打乱`s`中字符出现的相对顺序**。

  要求三、在所有符合上一条要求的去重字符串中，**字典序最小**的作为最终结果。

- 思路

  - 当x进栈时，发现栈顶的字符y比他大，此时，如果后面还有y，那已经进去的y应该之后再入栈（为了字典序最小）
    - 所以需要对每个字符进行**计数**
  - 如果字符c进栈，发现栈里已经有c了，那么新的c可以直接跳过
    - 原因：唯一一个不把新c跳过的理由，是“把c的位置从老c -> 新c，可以使得整体字典码变小”

  <img src="labuladong.assets/717117DF61C627F5C7F4EBB5846CC4D5.png" alt="img" style="zoom: 10%;" />

  <img src="labuladong.assets/1097EED96F23EE77B8F4D0984068C1FE.png" alt="img" style="zoom: 10%;" />

- 代码

  ```c++
  class Solution {
  public:
      string removeDuplicateLetters(string s) {
  		int exist[256];
  		int cnt[256];
  		stack<char> ans;
  		string ans_s;
  
  		for(int i=1;i<=255;++i){
  			exist[i]=0;
  			cnt[i]=0;
  		}
  		for(auto i:s){
  			cnt[i]++;
  		}
  		for(auto c:s){
  			cnt[c]--;
  			if(exist[c]==1) continue;
  			exist[c]=1;
  			while(!ans.empty() && ans.top()>c && cnt[ans.top()]>0){
  				// cnt[ans.top()]--;  // 错误，扣除重复了。每个字符，只在进栈时，扣除cnt就可以
  				exist[ans.top()]=0; // 漏了
  				ans.pop();
  			}
  			ans.push(c);
  		}
  		while(!ans.empty()){
              // 出栈时，反着排
  			ans_s=ans.top()+ans_s;
  			ans.pop();
  		}
  		return ans_s;
      }
  };
  ```

## 1.3 数据结构设计

没学，https://mp.weixin.qq.com/s/00yRZUaU5c5KV-yTWhoDig，之后学吧

# 2 进阶数据结构

## 2.1 二叉树

### 2.1.0 Overview

- Full Binary Tree，完满二叉树

  - 只要有孩子，就必然有两个孩子

  <img src="labuladong.assets/image-20220323220542342.png" alt="image-20220323220542342" style="zoom:50%;" />

- Complete Binary Tree，完全二叉树

  - 差最后一层的最右边

    <img src="labuladong.assets/image-20220323220644141.png" alt="image-20220323220644141" style="zoom:50%;" />

- Perfect Binary Tree，满二叉树（完美二叉树）

  - 正三角形

    <img src="labuladong.assets/image-20220323220731280.png" alt="image-20220323220731280" style="zoom:50%;" />

- 前序遍历：**pre**order

  中序遍历：**in**order

  后序遍历：**post**order

- **二叉树怎样序列化才能重建**

  - 存NULL

    - 前序、后序：可以。见2.1.3.9
    - 中序：**不可以**。经证明，所有二叉树的中序遍历都形如 
      - **单独的中序二叉树，不提供任何结构信息**

  - 不存NULL

    - 先序+中序：可以。

      - 要求是**树中没有重复的元素**，否则无法通过中序锁定root的位置

    - 后序 + 中序：可以

    - 先序+后序：**不可以**

      - 反例：下面两棵树的先序遍历都是 12，后序遍历都是 21。

        <img src="labuladong.assets/image-20220324205244357.png" alt="image-20220324205244357" style="zoom: 50%;" />

    - 层序 + 先序：**不可以**

      - 反例：上图

    - 层序 + 后序：**不可以**

      - 反例：上图

    - 层序 + 中序：可以

### 2.1.1 前中后序

- **前中后序**是遍历二叉树过程中处理每一个节点的**三个特殊时间点**，绝不仅仅是三个顺序不同的 List
  - **前**序位置的代码在**刚刚进入**一个二叉树节点的时候执行
  - **后**序位置的代码在**将要离开**一个二叉树节点的时候执行
  - **中**序位置的代码在一个二叉树节点左子树都遍历完，即将开始遍历右子树的时候执行。

- 框架

  ```c++
  void traverse(TreeNode root) {
      if (root == null) {
          return;
      }
      // 前序位置
      traverse(root.left);
      // 中序位置
      traverse(root.right);
      // 后序位置
  }
  ```

  <img src="labuladong.assets/image-20220314003250549.png" alt="image-20220314003250549" style="zoom:50%;" />

- 核心思路：**二叉树的所有问题，就是让你在前中后序位置注入巧妙的代码逻辑，去达到自己的目的**。只需要思考每一个节点应该做什么，其他的不用管，抛给二叉树遍历框架，递归会对所有节点做相同的操作。

### 2.1.2 后序遍历的特殊性

- **前序位置**的代码**只能**从函数参数中获取**父节点**传递来的数据，而**后序位置**的代码不仅可以获取**参数数据**，还可以获取到**子树通过函数返回值传递回来的数据**。
- 一旦你发现**题目和子树有关**，那大概率要给函数设置合理的定义和返回值、**在后序位置写代码了**。

### 2.1.3 递归 实战

#### 2.1.3.1 二叉树的最大深度

- 法1

  - 提问：为什么要在前序位置增加`depth`，在后序位置减小`depth`？
    - ans：前序位置是进入一个节点的时候，后序位置是离开一个节点的时候，`depth`记录当前递归到的节点深度，所以要这样维护

  ```c++
  // 记录最大深度
  int res = 0;
  // 记录目前节点的深度
  int depth = 0;
  
  // 主函数
  int maxDepth(TreeNode root) {
      traverse(root);
      return res;
  }
  
  // 二叉树遍历框架
  void traverse(TreeNode root) {
      if (root == null) {
          // 到达叶子节点，更新最大深度
          res = Math.max(res, depth);
          return;
      }
      // 前序位置
      depth++;
      // 中序位置
      traverse(root.left);
      traverse(root.right);
      // 后序位置
      depth--;
  }
  ```

- 法2

  - NULL时需要返回0，而不是1

    <img src="labuladong.assets/D3D9D8110A56D368FEF5C29FE78F2A60.png" alt="img" style="zoom: 25%;" />

  ```c++
  // 定义：输入根节点，返回这棵二叉树的最大深度
  int maxDepth(TreeNode* root) {
      if (root == NULL) {
          return 0;
      }
      // 利用定义，计算左右子树的最大深度
      int leftMax = maxDepth(root->left);
      int rightMax = maxDepth(root->right);
      // 整棵树的最大深度等于左右子树的最大深度取最大值，
      // 然后再加上根节点自己
      int res = max(leftMax, rightMax) + 1;
  
      return res;
  }
  ```

#### 2.1.3.2 后序遍历特殊性

- 二叉树的最大直径

  - leecode：543

  - 二叉树的「直径」长度，就是任意两个结点之间的路径长度。

  - 思路

    - 对每个节点做如下计算
      - 求出：”以当前节点为root，由left通过root，再通过right“的最大路程max_i
    - 在所有节点的max_i中，取最大，即是全图的最大

  - 解释

    - 无论「直径」长什么样，一定存在一个“最高点”。那把这个“最高点”作为root，则满足上述求解过程。所以一定能被上述过程求出。
    - 还有一个“恰巧”，就是上述答案，恰好等于“左深度+右深度”

  - 代码

    ```c++
    class Solution {
        int ans;
        int depth(TreeNode* root){
            if (root == NULL) return 0;
            int L = depth(root->left);
            int R = depth(root->right);
            ans = max(ans, L + R);
            return max(L, R) + 1;
        }
    public:
        int diameterOfBinaryTree(TreeNode* root) {
            ans = 0;
            depth(root);
            return ans;
        }
    };
    ```

#### 2.1.3.3 翻转二叉树

- 把一个二叉树镜像

  - leecode：226

    ```c++
    // 将整棵树的节点翻转
    TreeNode invertTree(TreeNode root) {
        // base case
        if (root == null) {
            return null;
        }
    
        /**** 前序遍历位置 ****/
        // root 节点需要交换它的左右子节点
        TreeNode tmp = root.left;
        root.left = root.right;
        root.right = tmp;
    
        // 让左右子节点继续翻转它们的子节点
        invertTree(root.left);
        invertTree(root.right);
    
        return root;
    }
    ```

#### 2.1.3.4 填充二叉树节点右侧指针

- 题意

  - leecode：116

  ![image-20220324113113100](labuladong.assets/image-20220324113113100.png)

- 只用一个参数解决不了，得两个

  - 直观感受：可以递归的，只能包含**一层与下一层**间的东西。
    - 如果用connect函数递归的话，会关系到下下层的点，所以不行。

  ```c++
  // 主函数
  Node connect(Node root) {
      if (root == null) return null;
      connectTwoNode(root.left, root.right);
      return root;
  }
  
  // 定义：输入两个节点，将它俩连接起来
  void connectTwoNode(Node node1, Node node2) {
      if (node1 == null || node2 == null) {
          return;
      }
      /**** 前序遍历位置 ****/
      // 将传入的两个节点连接
      node1.next = node2;
  
      // 连接相同父节点的两个子节点
      connectTwoNode(node1.left, node1.right);
      connectTwoNode(node2.left, node2.right);
      // 连接跨越父节点的两个子节点
      connectTwoNode(node1.right, node2.left);
  }
  ```

#### 2.1.3.5 二叉树拉平为链表

- 题意

  - leecode：114

    ![image-20220324151136544](labuladong.assets/image-20220324151136544.png)

- 收获

  - 递归的时候，**并不是只能触碰自己与下面一层，而是可以触碰很多**

  - 思路：递归

    - 先把左子树拉平
    - 再把右子树拉平
    - 左子树换到右边，把右子树接到左子树后面

    ```c++
    // 定义：将以 root 为根的树拉平为链表
    void flatten(TreeNode root) {
        // base case
        if (root == null) return;
    
        flatten(root.left);
        flatten(root.right);
    
        /**** 后序遍历位置 ****/
        // 1、左右子树已经被拉平成一条链表
        TreeNode left = root.left;
        TreeNode right = root.right;
    
        // 2、将左子树作为右子树
        root.left = null;
        root.right = left;
    
        // 3、将原先的右子树接到当前右子树的末端
        TreeNode p = root;
        while (p.right != null) {
            p = p.right;
        }
        p.right = right;
    }
    ```

#### 2.1.3.6 构建最大二叉树

- 题意

  - 把一个数组构建成了一个类似中序遍历的样子

    ![image-20220324154024707](labuladong.assets/image-20220324154024707.png)

    ![image-20220324154033110](labuladong.assets/image-20220324154033110.png)

- 这个题需要折腾一个**数组**，所以递归的时候，将**数组的左右区间作为参数**会比较舒服

  - 1

  ```c++
  /* 主函数 */
  TreeNode constructMaximumBinaryTree(int[] nums) {
      return build(nums, 0, nums.length - 1);
  }
  
  /* 将 nums[lo..hi] 构造成符合条件的树，返回根节点 */
  TreeNode build(int[] nums, int lo, int hi) {
      // base case
      if (lo > hi) {
          return null;
      }
  
      // 找到数组中的最大值和对应的索引
      int index = -1, maxVal = Integer.MIN_VALUE;
      for (int i = lo; i <= hi; i++) {
          if (maxVal < nums[i]) {
              index = i;
              maxVal = nums[i];
          }
      }
  
      TreeNode root = new TreeNode(maxVal);
      // 递归调用构造左右子树
      root.left = build(nums, lo, index - 1);
      root.right = build(nums, index + 1, hi);
  
      return root;
  }
  ```

#### 2.1.3.7 前序和中序构造二叉树

- 题意
  - leecode：105
  - 如何用前序和中序（两个数组）构建出原树
- 思考
  - 既然是跟数组打交道，那递归函数的参数一定是**数组的边界**
  - 可以通过preorder找到此时的根，于是可以通过inorder锁定左右子树
  - **难点在于**：构造左右子树时，数组边界怎么取

- 解题

  - 首先，中序遍历的边界很好取

    <img src="labuladong.assets/image-20220324155915112.png" alt="image-20220324155915112" style="zoom:50%;" />

    ```c++
    root.left = build(preorder, ?, ?,
                      inorder, inStart, index - 1);
    
    root.right = build(preorder, ?, ?,
                       inorder, index + 1, inEnd);
    ```

  - 然后，通过inorder找到left和right的长度，从而算出preorder中的长度怎么取

    - 如果倒腾长度太麻烦，可以这样想：以left为例，*(index-1)-inStart=x-(preStart+1)* 一定成立，解得x=preStart+（index - inStart）

    <img src="labuladong.assets/image-20220324160357719.png" alt="image-20220324160357719" style="zoom:50%;" />

    ```c++
    int leftSize = index - inStart;
    
    root.left = build(preorder, preStart + 1, preStart + leftSize,
                      inorder, inStart, index - 1);
    
    root.right = build(preorder, preStart + leftSize + 1, preEnd,
                       inorder, index + 1, inEnd);
    ```

- 代码

  - 1

  ```java
  TreeNode build(int[] preorder, int preStart, int preEnd, 
                 int[] inorder, int inStart, int inEnd) {
  
      if (preStart > preEnd) {
          return null;
      }
  
      // root 节点对应的值就是前序遍历数组的第一个元素
      int rootVal = preorder[preStart];
      // rootVal 在中序遍历数组中的索引
      int index = 0;
      for (int i = inStart; i <= inEnd; i++) {
          if (inorder[i] == rootVal) {
              index = i;
              break;
          }
      }
  
      int leftSize = index - inStart;
  
      // 先构造出当前根节点
      TreeNode root = new TreeNode(rootVal);
      // 递归构造左右子树
      root.left = build(preorder, preStart + 1, preStart + leftSize,
                        inorder, inStart, index - 1);
  
      root.right = build(preorder, preStart + leftSize + 1, preEnd,
                         inorder, index + 1, inEnd);
      return root;
  }
  ```

#### 2.1.3.8 二叉树中重复子树

- 题意

  - leecode：652
  - <img src="labuladong.assets/image-20220324162250107.png" alt="image-20220324162250107" style="zoom:50%;" />

- 思路

  - 对于递归中的某个root，他需要知道两个东西：

    1、以我为根的这棵二叉树（子树）长啥样？

    - 很好解决，通过后序遍历，把子树变为string

    2、以其他节点为根的子树都长啥样？

    - 需要一个全局信息，记录不同的string是否出现过

  - 哪怕一个子树出现了10次，也只应该把他加入答案集合1次。所以不仅需要知道“不同的string是否出现过”，还得知道“我是第几次出现”

- 题解

  - 1

  ```c++
  class Solution {
  public:
      string dfs(TreeNode* root, vector<TreeNode*>& res, unordered_map<string, int>& mp){
          if(root==NULL) return "";
          //二叉树先序序列化
          string str = to_string(root->val) + "," + dfs(root->left, res, mp) + "," + dfs(root->right, res, mp);
          
          if(mp[str]==1){
              res.push_back(root);
          } 
          mp[str]++;
          return str;
      }
  
      vector<TreeNode*> findDuplicateSubtrees(TreeNode* root) {
          vector<TreeNode*> res;
          unordered_map<string, int> mp;
          dfs(root, res, mp);
          return res;
      }
  
  };
  ```

#### 2.1.3.9 二叉树的序列化与反序列化

- 题意

  - leecode：297
  - 自己实现两个函数，可以把二叉树序列化再还原

- **前序遍历 方法**

  - 一般情况，只靠前序遍历是不足够还原出一棵树的。但是如果还存了NULL，那就可以了

  - 只要在遇到#时，return，就可以结束一侧的搜索

  - 过程如图

    <img src="labuladong.assets/EE0D16A85AC9548F5E5077A6589FBD94.png" alt="img" style="zoom: 33%;" />

    <img src="labuladong.assets/5DEA738930FA738AE5C4C44FEB3DCAC2.png" alt="img" style="zoom:33%;" />

  - 代码

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class Codec {
    private:
        string str;
    public:
    
        // Encodes a tree to a single string.
        string serialize(TreeNode* root) {
            rserialize(root);
            return str;
        }
    
        void rserialize(TreeNode* root){
            if(root==nullptr){
                str+="None,";
                return;
            }
            str+=to_string(root->val)+',';
            rserialize(root->left);
            rserialize(root->right);
        }
    
        TreeNode* rdeserialize(list<string>& dataArray){
            if(dataArray.front()=="None"){
                dataArray.erase(dataArray.begin());
                return nullptr;
            }
            TreeNode* root=new TreeNode(stoi(dataArray.front()));
            dataArray.erase(dataArray.begin());
            root->left=rdeserialize(dataArray);
            root->right=rdeserialize(dataArray);
            return root;
        }
    
    
        // Decodes your encoded data to tree.
        TreeNode* deserialize(string data) {
            list<string> dataArray;
            stringstream ss;
            ss.str(data);
            char delim=',';
            string item;
            while(getline(ss,item,delim)){
                dataArray.push_back(item);
            }
            
            return rdeserialize(dataArray);
        }
    };
    ```

- **后序遍历 方法**

  - 如果不经思考，认为后序遍历的deserialize也是后序遍历的板子，则会考虑如下的**错误代码**

    - 其错误显而易见，程序会陷入死循环

    ```java
    // 代码是错误的 代码是错误的 代码是错误的
    /* 辅助函数，通过 nodes 列表构造二叉树 */
    TreeNode deserialize(LinkedList<String> nodes) {
        if (nodes.isEmpty()) return null;
    	TreeNode root;
        root.left = deserialize(nodes);
        root.right = deserialize(nodes);
    
        /****** 后序遍历位置 ******/
        String first = nodes.removeFirst();
        if (first.equals(NULL)) return null;
        root = new TreeNode(Integer.parseInt(first));
        /***********************/
    
        return root;
    }
    // 代码是错误的 代码是错误的 代码是错误的
    ```

  - 正确的思路：

    - 每次拿出**最后一个元素**，作为根节点
    - 然后用剩余的点先构建右子树；再构建左子树

    <img src="labuladong.assets/4A7CDDB5C72DB986D9BA12705189CC33.png" alt="img" style="zoom: 33%;" />

    ​	

  - java版

    ```c++
    /* 主函数，将字符串反序列化为二叉树结构 */
    TreeNode deserialize(String data) {
        LinkedList<String> nodes = new LinkedList<>();
        for (String s : data.split(SEP)) {
            nodes.addLast(s);
        }
        return deserialize(nodes);
    }
    
    /* 辅助函数，通过 nodes 列表构造二叉树 */
    TreeNode deserialize(LinkedList<String> nodes) {
        if (nodes.isEmpty()) return null;
        // 从后往前取出元素
        String last = nodes.removeLast();
        if (last.equals(NULL)) return null;
        TreeNode root = new TreeNode(Integer.parseInt(last));
        // 限构造右子树，后构造左子树
        root.right = deserialize(nodes);
        root.left = deserialize(nodes);
    
        return root;
    }
    ```

- 

## 2.2 二叉搜索树

## 2.3 图论

### 2.3.1 最短路算法

- 整体摘自这个博客：

  [最短路的三种算法（Floyd、Dijkstra、SPFA）]: https://blog.csdn.net/qq_36932169/article/details/78806863

- Floyd算法是**多源**最短路算法，复杂度最高O(v^3)，通常用在点比较少的起点不固定的问题中。**能解决负边（负权）但不能解决负环**。
- Dijkstra算法是**单源**最短路算法，最常用时间复杂度（n\^2）优化后可以达到（nlogn），不能解决负边问题，稀疏图（点的范围很大但是边不多，边的条数|E|远小于|V|²）需要耗费比较多的空间。
- SPFA算法适合稀疏图，可以解决带有负权边，负环的问题，但是在稠密图中效率比Dijkstra要低。
  三种方法各有优劣适合的情况不同，所以可以判断情况选择一个适合的算法也是ACMer的基本素质。

#### 2.3.1.1 Floyd算法

- k：考虑 让所有“节点编号<=k”的节点作为“中转站”优化

  - O(v^3)

  - 代码

    ```c++
    #define MAX 65535
    int Chara[N][N];// Chara为邻接矩阵
    int p[N][N];  // p为前驱矩阵，“i到j的最短路，应该让i先去p[i][j]中转”
    int n,m;
    
    void Floyd()
    {
       for(int i=0;i<n;i++)
          for(int j=0;j<n;j++)
              p[i][j]=j;//初始化，认为i到j的最短路 只能是 i直接到j
        
        for(int k=0;k<n;k++)
            for(int i=0;i<n;i++)
                for(int j=0;j<n;j++){
                    if(Chara[i][k] == MAX || Chara[k][j] == MAX) continue;
                    if(Chara[i][j] > Chara[i][k] + Chara[k][j]){
                        //如果经过下标k顶点路径比原两点间路径更短
                        //将当前两点权值设为更小的那一个
                          Chara[i][j] = Chara[i][k] + Chara[k][j];
                          p[i][j]=p[i][k];//路径设置经过下标k的顶点
                    }
                }
    ```

- 原理探讨：https://blog.csdn.net/xianpingping/article/details/79947091?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-0.pc_relevant_default&spm=1001.2101.3001.4242.1&utm_relevant_index=3

  - 简单来说：

    > 我们需要证明一个很致命的结论:
    >
    > 假设i和j之间的最短路径上的结点集里(不包含i,j),编号最大的一个是x.那么在外循环k=x时,d[i][j]肯定得到了最小值.
    >
    > 
    >
    > 怎么证明,可以用强归纳法.
    >
    > 设i到x中间编号最大的是x1,x到j中间编号最大的是x2.
    >
    > 由于x是i到j中间编号最大的,那么显然x1<x,x2<x.
    >
    > 根据结论,k=x1的时候d[i][x]已经取得最小值,k=x2的时候d[x][j]已经取得最小值.
    >
    > 那么就是k=x的时候,d[i][x]和d[x][j]肯定都已经取得了最小值.
    >
    > 因此k=x的时候,执行d[i][j]=min(d[i][j],d[i][x]+d[x][j])肯定会取得d[i][j]的最小值.
    >
    > 证毕.

  - 举例：

    > 1->4->3->2，当k=4时，才能得到 1到2 的最短路，但 4到2 的最短路已经在k=3的时候得出，于是在k=4时，能得到 1到4到2 的最短路。这也印证了上述论证：4是1到2的路径中最大的，当k=4时，4的左边和右边均已优化完毕。

#### 2.3.1.2 Dijkstra算法

- 加入贪心策略的Floyd算法（不过Floyd是多源的，Dijkstra是单源的）

  - 思路

    1. 有两个数组，dis和vis含义参见上面，初始时vis中只有起点，更新dis中的起点到所有点的距离
    2. 遍历所有节点，找到**距离起点最近的**一个点K，将这个点加入vis中标记
    3. 进行松弛操作，遍历没有在vis数组中的其他所有点，比较 “起点->该点” 和 “起点->K点->该点” 的距离
    4. 重复2-3操作，直到所有的点遍历完

  - 代码

    ```c++
    #define INF 65535
    int n,m,s,t;
    // Chara:邻接矩阵，dis[i]:src到i点的最短路程
    // vis[i]:i点是否考虑过了,p[i]:i点的前驱节点是p[i]
    int Chara[N][N],dis[N],vis[N],p[i];
    
    void Dijkstra(int src)  //src传入的起点
    {
        for(int i=0; i<m; i++) //初始化起点到所有点的距离
        {
            dis[i] = Chara[src][i];
            vis[i] = 0;
            p[i]=0;
        }
        dis[src] = 0; //到自身距离为0
        vis[src] = 1; //标记 注src=0
        for(int i=0; i<m; i++)
        {
            int ans = INF,k;
            for(int j=0; j<m; j++) // 寻找 未被访问过&&距离起点v0最近 的点
            {
                if(!vis[j] && dis[j] < ans)
                {
                    k = j;
                    ans = dis[j];
                }
            }
            vis[k] = 1;   //标记已访问
            if(ans == INF) break; //表示剩下所有点都不通
            for(int j =0; j<m; j++)  //松弛操作，更新起点到所有未访问点的距离
            {
                if(!vis[j] &&  dis[k] + Chara[k][j]<dis[j] )
                {
                    dis[j] = dis[k] + Chara[k][j];
                    p[j]=k;//存放前驱节点
                }
            }
        }
    }
    ```

#### 2.3.1.3 SPFA算法

- 队列优化的B-F算法。可以解决负边问题，可以判断负环是否存在。在稀疏图中，采用类似邻接链表储存比较节省空间。

- 代码

  - weight是路径长度

  ```c++
  const int INF=0x3f3f3f3f;
  const int N=210;
  int n,m,s,t;
  int dis[N],vis[N],sum[N];
  struct node{
      int v; ///点
      int weight; ///权值
  };
  vector<node>mp[N]; //储存边;
  //SPFA写法
  void SPFA(int src)
  {
      int q;
      queue<int>Q;
      vis[src] = 1;
      dis[src] = 0;
      Q.push(src);
      while(!Q.empty())
      {
          q = Q.front();
          Q.pop();
          vis[q] = 0;
          for(int i=0;i<mp[q].size();i++)
          {
              if(dis[q] + mp[q][i].weight < dis[mp[q][i].v])
              {
                  dis[mp[q][i].v] = dis[q] + mp[q][i].weight;
                  if(!vis[mp[q][i].v])
                  {
                      Q.push(mp[q][i].v);
                      vis[mp[q][i].v] = 1;
                  }
              }
          }
      }
      return ;
  }
  ```

- 思路

  - 初始时，只有把起点放入队列中。
  - 遍历与起点相连的边，如果可以松弛就更新距离dis[],然后判断如果这个点没有在队列中就入队标记。
    出队队首，取消标记，循环2-3步，直至队为空。
  - 所有能更新的点都更新完毕，dis[]数组中的距离就是，起点到其他点的最短距离。

- 为什么SPFA可以**处理负边**

  - 因为在SPFA中每一个点松弛过后说明这个点距离更近了，所以有可能通过这个点会再次优化其他点，所以将这个点入队再判断一次，而Dijkstra中是贪心的策略，每个点选择之后就不再更新，如果碰到了负边的存在就会破坏这个贪心的策略就无法处理了。

- 如何判断**成环**

  - 在储存边时，记录下每个点的入度，每个点入队的时候记录一次，如果入队的次数大于这个点的入度，说明从某一条路进入了两次，即该点处成环。

### 2.3.2 费用流



# 暴力搜索算法

## DFS 算法/回溯算法

## BFS 算法

# 动态规划

## 动态规划核心原理

## 经典动态规划

## 背包问题

## 用动态规划玩游戏

# 其他经典算法

## 数学算法

## 面试必知必会



st表、线段树 逆天