# LeetCode题解合集
  * 程序实现参考[https://github.com/JDreamHeart/DailyCodes/csharp/LeetCode](https://github.com/JDreamHeart/DailyCodes/tree/master/csharp/LeetCode)

----
合集内容：  
  * [三数之和](#三数之和)
  * [两个有序数组的中位数](#两个有序数组的中位数)

## 三数之和
```csharp
// 给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。

// 注意：答案中不可以包含重复的三元组
```
思路：  
  * (1)题目中明确规定要三个元素的组合，因此得先判定传入参数是否足够三个元素；
  * (2)相对于查找无序的数组来说，从有序的数组中进行查找，比较有迹可循；【因此最好对传入的数组参数进行排序（这里进行升序排序）】
  * (3)排完序后的数组，如果其头部大于总和，或尾部小于总和，则判定不存在有效结果；
  * (4)从数组的头部开始，一直遍历到数组的倒数第三个数据，该位置定义为`i`；【需要注意的是得确保第一个数不能大于总和，且新位置的值不等于旧值（为了避免重复）】
  * (5)然后定义后面两个元素，先是初始化中间位置（`j`初始化为`i`的下一位置）和第三个数的位置（`k`初始化为数组的最后一个）
  * (6)在保证`j`的位置一直在`k`前面的情况下，每当三数之和小于0，则将`j`位置往后偏移1位；每当三数之和大于0，则将`k`位置往前偏移1位；每当三数之和等于0，则进行下一步处理；【需要注意的是得确保最后一个数不能小于总和】
  * (7)当三数之和等于0，则将数据添加到结果中，并更新`j`和`k`（仍需确保`j`的位置一直在`k`前面，且两个新位置的值不等于旧值（为了避免重复））；

Github链接：[https://github.com/JDreamHeart/DailyCodes/csharp/LeetCode/Solution6.cs](https://github.com/JDreamHeart/DailyCodes/tree/master/csharp/LeetCode/Solution6.cs)


## 两个有序数组的中位数
```csharp
// 给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。

// 请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

// 你可以假设 nums1 和 nums2 不会同时为空。
```
思路：  
  * (1)题目中要求了算法复杂度，一般要求时间复杂度为`O(log(n))`的，基本上都是使用二分的思想进行求解；
  * (2)先根据数组的大小，来获取两个数组中的中位数所在位置；
  * (3)将较小位置数值作为间隔，来得到两个数组分别跟头和尾相同间隔的元素值（`h1,r1,h2,r2`）；比较`h1`和`h2`数值，删除较小的值在对应数组中到头部的元素；比较`r1`和`r2`数值，删除较大的值在对应数组中到尾部的元素；
  * (4)然后从(2)重新开始，直到其中一个数组没有元素或只有一个元素；
  * (5)当其中一个数组中没有元素时，返回另一个数组的中位数即可；
  * (6)当其中一个数组中只有一个元素时，则依次比较另一个数组的头尾数值（大于头部，则去除头部；小于尾部，则去除尾部），当其中一个数组没有元素时，返回另一个数组的中位数即可；
  
Github链接：[https://github.com/JDreamHeart/DailyCodes/csharp/LeetCode/Solution7.cs](https://github.com/JDreamHeart/DailyCodes/tree/master/csharp/LeetCode/Solution7.cs)

## 正则表达式匹配
```csharp
// 给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。

// '.' 匹配任意单个字符
// '*' 匹配零个或多个前面的那一个元素
// 所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。

// 说明:

// s 可能为空，且只包含从 a-z 的小写字母。
// p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *。
```
有三种方法：  
  * 分段匹配法。把任何一个给定的`p`字符串 分割为“尾星串”和“无星串”的组合，然后依次匹配即可。
  * 回溯法。以`p`为主串，从左向右去匹配`s`，匹配成功的部分都去掉，也就是说 若最终`s`与`p`都变成了空串，则匹配成功。
  * 动态规划法。

回溯法：  
出口和递归分支`->`  
  * 出口EXIT：p变成空串时，若s也变成了空串，则匹配成功，否则匹配失败。
  * 分支A：p[1]为星号，直接去掉p的前两位，并递归。如 s="b",p="a*b".
  * 分支B：p[1]为星号时，若s第一位与p第一位匹配，去掉s第一位 , 并递归，如 "s=aab",p="a*b"。否则匹配失败，如 s="bba",p="a*b".
  * 分支C：p[1]不为星号时，若s与p第一位匹配成功, 则都去掉第一位,并递归，如 s="aab",p="aab*". 否则匹配失败，如 s="bab", p="aab*" .

其中，当p[1]为星号时，分支A与分支B是【或】的关系，只要有一条成功，则匹配成功； 当p[1]不为星号时，就走C。  


动态规划法：  
table[0,0]=true表示s与p是匹配的，而且在table种我们可以得到一条通路from右下角to左上角（忽略2个星列），这条通路的走向有3种：
  * 1) 格星左跳一位，例如table[4,6]到table[4,4],  【A】
  * 2) 向上走一位，例如tble[3,1]到table[2,1], 【B】
  * 3) 向左上走一位，例如 table[1,1]到 table[0,0] , 【C】


## 两两交换链表中的节点
循环往复的进行两两交换，直至最后没有next节点了。  

交换的逻辑是：  
  * 第一个节点的`next`指向第二个节点的`next`，如`1->3（3是2的next)`
  * 第二个节点的`next`指向第一个节点，如`2->1`，此时结果是`2->1->3`
  * 将`pre`节点的`next`指向上面子链表的头结点，也就是第二个节点


```csharp
// 给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

// 你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

// 示例:

// 给定 1->2->3->4, 你应该返回 2->1->4->3.

public class Solution {
    public ListNode SwapPairs(ListNode head) {
        if(head == null || head.next == null)
        {
            return head;
        }

        ListNode dummy = new ListNode(0);
        dummy.next = head;

        ListNode pre = dummy;
        ListNode first = head;
        ListNode second = head.next;
        while(first != null && second !=null)
        {
            first.next = second.next;
            second.next = first;
            pre.next = second;

            pre = first;
            first = first.next;
            if(first != null )
            {
                second = first.next;
            }
        }

        return dummy.next;
        
    }
}
```

## K 个一组翻转链表
```csharp
// 给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。

// k 是一个正整数，它的值小于或等于链表的长度。

// 如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

// 示例：

// 给你这个链表：1->2->3->4->5

// 当 k = 2 时，应当返回: 2->1->4->3->5

// 当 k = 3 时，应当返回: 3->2->1->4->5

// 说明：
// 你的算法只能使用常数的额外空间。
// 你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

public class Solution
{
        public ListNode ReverseKGroup(ListNode head, int k)
    {
        List<ListNode> list = new List<ListNode>();
        ListNode p = head;
        for (int i = 0; i < k; i++)
        {
            if (head == null) { return p; }
            list.Add(head);
            head = head.next;
        }
        for (int i = 1; i < k; i++)
        {
            list[i].next = list[i - 1];
        }
        list[0].next = ReverseKGroup(head, k);
        return list[k - 1];
    }
}
```

## 两数相除
```csharp
// 给定两个整数，被除数 dividend 和除数 divisor。将两数相除，要求不使用乘法、除法和 mod 运算符。

// 返回被除数 dividend 除以除数 divisor 得到的商。

// 整数除法的结果应当截去（truncate）其小数部分，例如：truncate(8.345) = 8 以及 truncate(-2.7335) = -2

// 示例 1:

// 输入: dividend = 10, divisor = 3
// 输出: 3
// 解释: 10/3 = truncate(3.33333..) = truncate(3) = 3
// 示例 2:

// 输入: dividend = 7, divisor = -3
// 输出: -2
// 解释: 7/-3 = truncate(-2.33333..) = -2

// 提示：

// 被除数和除数均为 32 位有符号整数。
// 除数不为 0。
// 假设我们的环境只能存储 32 位有符号整数，其数值范围是 [−231,  231 − 1]。本题中，如果除法结果溢出，则返回 231 − 1。
```
减去一个2的幂次，不能用乘法运算，但是可以用移位运算来形成2的幂次，然后使用被除数减去2的幂次来加快计算效率，实际执行如下：（search递归函数）  
  * 被除数 = 除数 * 2^a + b
  * 满足公式的最大a，然后递归将b作为被除数
  * 直到被除数小于除数为止
  * 将所有的2^a进行求和

另外，为了防止整数溢出的情况，使用了负数进行减法运算。

```csharp
public class Solution {

    public int search(int m,int n)
    {
        if(m > n) return 0;//当被除数比除数的绝对值还小的时候结束

        int k = -1, t = n;
        while(m < t && m - t < t)
        {//使用2的幂次进行求解
            t <<= 1;
            k <<= 1;
        }
        return  k + search(m - t,n);
    }

    public int search2(int m, int n) {
        int r=0, k = 0;
        while(m<n && m-n<=n)
        {//找最大的幂次
            n <<= 1;
            k ++;
        }
        while(k>=0)
        {
            if(m<=n)
            {//第k个二进制位为1
                m -= n;
                r += (-1 << k);
            }
            n >>= 1;
            k--;
        }
        return r;
    }

    public int Divide(int dividend, int divisor) {
        int mFlag = dividend>0?-1:1;
        int nFlag = divisor>0?-1:1;
        int flag = -1* mFlag * nFlag;
        //将值转换为负值进行计算
        int m = dividend * mFlag, n = divisor * nFlag;

        int r = search(m,n);

        // // 省去了递归的栈空间消耗
        // int r = search2(m,n);

        if(r == -2147483648 && flag == -1)
        {//只有一种情况溢出，就是最小负值转换为正值时
            return 2147483647;
        }

        return r * flag;
    }
}
```

**位运算**  
1) 异或（^）
```csharp
// 交换
int a=10100001, b=00000110;
a = a^b; 　　//a=10100111
b = b^a; 　　//b=10100001
a = a^b; 　　//a=00000110

// 快速判断两个值是否相等
return ((a ^ b) == 0)
```

2) 移位运算(左移和右移)
```csharp
x<<1 = x*2
x<<2 = x*4
x<<3 = x*8
x<<4 = x*16

x>>1 = x/2
x>>2 = x/4
x>>3 = x/8
x>>4 =x/16
```

**使用位运算实现加减乘除**  
加法：`a+b=a^b+(a&b)<<1`【进行异或运算，代表两个不考虑进位的加法运算；进行与运算后再往左移动一位，代表只考虑进位的两个数相加结果】  

减法：`a-b=a+~b+1`【一个数的相反数对于其各位取反加1，即a的相反数等于~a+1】  

乘法：将b个a相加  

除法：a最多能被多少个b减  


## 串联所有单词的子串
```csharp
// 给定一个字符串 s 和一些长度相同的单词 words。找出 s 中恰好可以由 words 中所有单词串联形成的子串的起始位置。

// 注意子串要与 words 中的单词完全匹配，中间不能有其他字符，但不需要考虑 words 中单词串联的顺序。

// 示例 1：

// 输入：
//   s = "barfoothefoobarman",
//   words = ["foo","bar"]
// 输出：[0,9]
// 解释：
// 从索引 0 和 9 开始的子串分别是 "barfoo" 和 "foobar" 。
// 输出的顺序不重要, [9,0] 也是有效答案。
// 示例 2：

// 输入：
//   s = "wordgoodgoodgoodbestword",
//   words = ["word","good","best","word"]
// 输出：[]
```

滑动窗口`=>`【一直在`s`维护着所有单词长度总和的一个长度队列】

```py
class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        from collections import Counter
        if not s or not words:return []
        one_word = len(words[0])
        word_num = len(words)
        n = len(s)
        if n < one_word:return []
        words = Counter(words)
        res = []
        for i in range(0, one_word):
            cur_cnt = 0
            left = i
            right = i
            cur_Counter = Counter()
            while right + one_word <= n:
                w = s[right:right + one_word]
                right += one_word
                if w not in words:
                    left = right
                    cur_Counter.clear()
                    cur_cnt = 0
                else:
                    cur_Counter[w] += 1
                    cur_cnt += 1
                    while cur_Counter[w] > words[w]:
                        left_w = s[left:left+one_word]
                        left += one_word
                        cur_Counter[left_w] -= 1
                        cur_cnt -= 1
                    if cur_cnt == word_num :
                        res.append(left)
        return res
```

## 下一个排列
```csharp
// 实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

// 如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

// 必须原地修改，只允许使用额外常数空间。

// 以下是一些例子，输入位于左侧列，其相应输出位于右侧列。
// 1,2,3 → 1,3,2
// 3,2,1 → 1,2,3
// 1,1,5 → 1,5,1
```

![NextPermutation](./img/next_permutation.gif)

```csharp
public class Solution {
    public void NextPermutation(int[] nums) {
        bool find = false;
        int index = 0;
        for(int i=nums.Length-1; i>0;i--){
            if(nums[i]>nums[i-1]){
                index = i-1;
                find = true;
                break;
            }
        }
        
        //说明全部是降序 进行翻转然后输出
        if(!find){ 
            SwitchLoHi(nums,0,nums.Length-1);
            return;
        }
        
        int index2 = 0;
        for(int j=nums.Length-1;j>index;j--){
            if(nums[j]>nums[index]){index2=j; break;}
        }
        
        Switch(nums, index, index2);
        SwitchLoHi(nums, index+1, nums.Length-1);
    }
    
    public void SwitchLoHi(int[] nums, int lo, int hi){
         while(lo<hi){
               Switch(nums, lo++,hi--);
         }
    }
    
    public void Switch(int[] nums, int i, int j){
        int m=0;
        m=nums[i];
        nums[i] = nums[j];
        nums[j]= m;
    }
}
```

## 最长有效括号
```csharp
// 给定一个只包含 '(' 和 ')' 的字符串，找出最长的包含有效括号的子串的长度。

// 示例 1:

// 输入: "(()"
// 输出: 2
// 解释: 最长有效括号子串为 "()"
// 示例 2:

// 输入: ")()())"
// 输出: 4
// 解释: 最长有效括号子串为 "()()"
```
**不需要额外的空间**  
利用两个计数器 left 和 right 。  
  * 首先，我们从左到右遍历字符串，对于遇到的每个‘(’，我们增加 left 计算器，对于遇到的每个‘)’ ，我们增加 right 计数器。
  * 每当 left 计数器与 right 计数器相等时，我们计算当前有效字符串的长度，并且记录目前为止找到的最长子字符串。如果 right 计数器比 left 计数器大时，我们将 left 和 right 计数器同时变回 0 。


**栈**  
  * 与找到每个可能的子字符串后再判断它的有效性不同，我们可以用栈在遍历给定字符串的过程中去判断到目前为止扫描的子字符串的有效性，同时能的都最长有效字符串的长度。我们首先将 -1 放入栈顶。
  * 对于遇到的每个 ‘(’ ，我们将它的下标放入栈中。
  * 对于遇到的每个 ‘)’ ，我们弹出栈顶的元素并将当前元素的下标与弹出元素下标作差，得出当前有效括号字符串的长度。通过这种方法，我们继续计算有效子字符串的长度，并最终返回最长有效子字符串的长度。

```csharp
public class Solution {

    public int longestValidParentheses(String s) {
        int maxans = 0;
        Stack stack = new Stack();
        stack.Push(-1);
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                stack.Push(i);
            } else {
                stack.Pop();
                if (stack.Count == 0) {
                    stack.Push(i);
                } else {
                    maxans = Math.max(maxans, i - stack.Peek());
                }
            }
        }
        return maxans;
    }
}
```

**动态规划**  
  * 定义一个 dp 数组，其中第 i 个元素表示以下标为 i 的字符结尾的最长有效子字符串的长度。
  * 将 dp 数组全部初始化为 0 。现在，很明显有效的子字符串一定以 ‘)’ 结尾。
  * 进一步可以得出结论：以 ‘(’ 结尾的子字符串对应的 dp 数组位置上的值必定为 0 。
  * 所以说只需要更新 ‘)’ 在 dp 数组中对应位置的值。

```csharp
public class Solution {
    public int longestValidParentheses(String s) {
        int maxans = 0;
        int dp[] = new int[s.length()];
        for (int i = 1; i < s.length(); i++) {
            if (s.charAt(i) == ')') {
                if (s.charAt(i - 1) == '(') {
                    dp[i] = (i >= 2 ? dp[i - 2] : 0) + 2;
                } else if (i - dp[i - 1] > 0 && s.charAt(i - dp[i - 1] - 1) == '(') {
                    dp[i] = dp[i - 1] + ((i - dp[i - 1]) >= 2 ? dp[i - dp[i - 1] - 2] : 0) + 2;
                }
                maxans = Math.max(maxans, dp[i]);
            }
        }
        return maxans;
    }
}
```


## 搜索旋转排序数组
```csharp
// 假设按照升序排序的数组在预先未知的某个点上进行了旋转。

// ( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

// 搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

// 你可以假设数组中不存在重复的元素。

// 你的算法时间复杂度必须是 O(log n) 级别。

// 示例 1:

// 输入: nums = [4,5,6,7,0,1,2], target = 0
// 输出: 4
// 示例 2:

// 输入: nums = [4,5,6,7,0,1,2], target = 3
// 输出: -1
```
利用中点左右的区间始终有一边有序的性质可以排除掉一边，如果目标在有序的一边，那么就是普通的二分。

```csharp
public class Solution {
    public int Search(int[] nums, int target) {
       int len = nums.Length;
       int l = 0;
       int r = len - 1;
       while(l <= r) {
          int mid = (l+r)>>1;
          if(nums[mid] == target) return mid; //排除mid位置方便书写
          if(nums[l] <= nums[r]) { //有序
              if(nums[mid] > target) {
                  r  = mid - 1;
              } else {
                  l = mid + 1;
              }
          } else { //无序
              if(nums[l] <= nums[mid] && nums[mid] > nums[r]) { //左边有序
                  if(target >= nums[l] && target < nums[mid]) { //在左边
                      r = mid-1;
                  } else {
                      l = mid +1;
                  }
              } else if(nums[l] >= nums[mid] && nums[mid] < nums[r]) { //右边有序
                  if(target > nums[mid] && target <= nums[r]) { //在右边
                      l = mid + 1;
                  } else {
                      r = mid - 1;
                  }
              } 
          }
       }
       return -1;
    }
}
```


## 在排序数组中查找元素的第一个和最后一个位置
```csharp
// 给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。

// 你的算法时间复杂度必须是 O(log n) 级别。

// 如果数组中不存在目标值，返回 [-1, -1]。

// 示例 1:

// 输入: nums = [5,7,7,8,8,10], target = 8
// 输出: [3,4]
// 示例 2:

// 输入: nums = [5,7,7,8,8,10], target = 6
// 输出: [-1,-1]
```
二分查找第一个大于target的位置，然后线性查找最后一个相等值的位置。

```csharp
public class Solution {
    public int[] SearchRange(int[] nums, int target) {
        int len = nums.Length;
        int[] ans = new int[]{-1,-1};
        if(len == 0) return ans;
        int l = 0;
        int r = len;
        while(l < r) {
            int mid = (l+r) >> 1;
            if(nums[mid] >= target) {
                r = mid;
            } else {
                l = mid + 1;
            }
        }
        if(r < len &&nums[r] == target) {
            ans[0] = r;
            for(; r < len; r ++) {
                if(nums[r] != target) break;
            }
            ans[1] = r-1;
            return ans; 
        } else {
            return ans;
        }
    }
}
```