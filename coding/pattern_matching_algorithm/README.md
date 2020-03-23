# 模式匹配算法

----
## 朴素的模式匹配算法
**算法思想：**
  * 从目标串的的第一个字符起与模式串的第一个字符比较，若相等，则继续对字符进行后续的比较，否则目标串从第二个字符起与模式串的第一个字符重新比较，直至模式串中的每个字符依次和目标串中的一个连续的字符序列相等为止，此时称为匹配成功，否则匹配失败。

**复杂度分析：**
若模式子串的长度是m,目标串的长度是n，这时最坏的情况是每遍比较都在最后出现不等，即每遍最多比较m次，最多比较n-m+1遍，总的比较次数最多为m(n-m+1)，因此朴素的模式匹配算法的时间复杂度为O(mn)。

## KMP匹配算法
**算法思想：**
  * 设目标串为s，模式串为t， i、j 分别为指示s和t的指针，i、j的初值均为0。
  * 若有 s[i] = t[j]，则i和j分别增1；否则，i不变，j退回至j=next[j]的位置 ( 也可理解为字符串s不动，模式串t向右移动到s[i]与t[`next[j]`]对齐 )；
  * 比较s[i]和t[j]，若相等则指针各增1；否则 j 再退回到下一个j=next[j]的位置(即模式串继续向右移动 )，再比较 s[i]和t[j]。
  * 依次类推，直到下列两种情况之一：
    * 1) j退回到某个j=next[j]时有 s[i] = t[j]，则指针各增1，继续匹配；
    * 2) j退回至 j=-1，此时令指针各增l，即下一次比较 s[i+1]和 t[0]。

**复杂度分析：**
对于长度为m的模式串和长度为n的目标字符串的模式匹配，KMP算法的时间复杂度为O(M+n)，其中M为获取对应模式串的next数组的复杂度。

**关于next数组：**
(1) 前缀与后缀法（时间复杂度O(m^2)）：  
在整个算法中，重点在于怎么来得到`next`数组。
  * 首先得了解："前缀"指除了最后一个字符以外，一个字符串的全部头部组合；"后缀"指除了第一个字符以外，一个字符串的全部尾部组合。
  * 然后`next`数组中对应位置的值（设为`v`），就是匹配串t从头到v的字符串，前缀与后缀的最长的共有元素的长度。

以匹配串`ABCABD`为例：
  * "A"的前缀和后缀都为空集，共有元素的长度为0；
  * "AB"的前缀为[A]，后缀为[B]，共有元素的长度为0；
  * "ABC"的前缀为[A, AB]，后缀为[BC, C]，共有元素的长度0；
  * "ABCA"的前缀为[A, AB, ABC]，后缀为[BCA, CA, A]，共有元素为"A"，长度为1；
  * "ABCAB"的前缀为[A, AB, ABC, ABCA]，后缀为[BCAB, CAB, AB, B]，共有元素为"AB"，长度为2；
  * "ABCABD"的前缀为[A, AB, ABC, ABCA, ABCAB]，后缀为[BCABD, CABD, ABD, BD, D]，共有元素的长度0；
因此匹配串`ABCABD`的next数组为`[0, 0, 0, 1, 2, 0]`。

对应该next数组的kmp算法：  
```csharp
int kmp(string s, string p) {
    int[] next = getNext(p);
    int j = 0;
    for (int i = 0; i < s.Length; i++) {
        for (; j < p.Length; j++) {
            if (s[i + j] != p[j]) {
                i += j > 0 ? (j - next[j - 1]) : 1;
                j = next[j > 0 ? j - 1 : 0];
                break;
            } else if (j == p.Length - 1) {
                return i;
            }
        }
    }
    return -1;
}
```


(2) 回溯法（时间复杂度O(m)）：  
在遍历匹配串时，逐步判断字符是否相同。
```csharp
int[] getNext(string s) {
    int i = 0, j = -1;
    int[] next = new int[s.Length];
    while (i < s.Length) {
        if (j == -1 || s[i] == s[j]) {
            i++;
            j++;
            next[i] = j;
        } else {
            j = next[j];
        }
    }
    return next;
}
```

对应该next数组的kmp算法：  
```csharp
int kmp(string s, string p) {
    int[] next = getNext(p);
    int j = 0;
    for (int i = 0; i < s.Length; i++) {
        for (; j < p.Length; j++) {
            if (s[i] != p[j]) {
                if (j == 0) {
                    i++;
                }
                j = next[j > 0 ? j - 1 : 0];
                break;
            } else if (j == p.Length - 1) {
                return i - j;
            }
        }
    }
    return -1;
}
```


## BM匹配算法
**算法思想：**
BM算法是一种精确字符串匹配算法（区别于模糊匹配）。采用从右向左比较的方法，同时应用到了两种启发式规则，即坏字符规则 和好后缀规则 ，来决定向右跳跃的距离。  
BM算法的基本流程: 设字符串S，模式串为P。首先将S与P进行左对齐，然后进行从右向左比较。  
若是某趟比较不匹配时，BM算法就采用两条启发式规则，即`坏字符规则`和`好后缀规则`，来计算模式串向右移动的距离，去较大的那个值进行移动，直到整个匹配过程的结束。  

**坏字符规则**
坏字符，指的是字符串S中与模式串P不匹配的字符。  
此时，<strong style="color:darkred;">模式串P的后移位数 = 坏字符对应模式串P的位置 - 模式串P中的上一次出现坏字符串位置</strong>【注意位置从0开始】。  
  
```csharp
// 构建坏字符
Dictionary<char, int> buildBc(string p) {
    Dictionary<char, int> ret = new Dictionary<char, int>();
    for (int i = 0; i < p.Length; i++) {
        ret[p[i]] = i;
    }
    return ret;
}
// 根据坏字符，获取模式串P的后移位数
int movebyBc(string subStr, string p, Dictionary<char, int> bc) {
    for (int i = subStr.Length; i >= 0; i++) {
        if (subStr[i] != p[i]) {
            // 坏字符在主串中的下标 - 模式串中对应的坏字符下标
            int idx = -1; // 模式串中对应的坏字符下标
            char c = subStr[i];
            if (bc.ContainsKey(c)) {
                idx = bc[c];
            }
            return i - idx;
        }
    }
    return -1; // 表示没有坏字符，在开始位置就匹配了
}
```

**好后缀规则**
好后缀，指当某个字符不匹配时，已成功匹配的那部分字符。  
此时，<strong style="color:darkred;">模式串P的后移位数 = 好后缀对应模式串P的位置 - 模式串P中的上一次出现好后缀位置</strong>【注意位置从0开始】。  

`好后缀的注意事项：`
  * `好后缀`的位置以最后一个字符为准。假定"ABCDEF"的"EF"是好后缀，则它的位置以"F"为准，即5（从0开始计算）
  * 如果`好后缀`在搜索词中只出现一次，则它的上一次出现位置为 -1。比如，"EF"在"ABCDEF"之中只出现一次，则它的上一次出现位置为-1（即未出现）
  * 如果`好后缀`有多个，则除了最长的那个`好后缀`，其他`好后缀`的上一次出现位置必须在头部。比如，假定"BABCDAB"的"好后缀"是"DAB"、"AB"、"B"，则此时采用的好后缀是"B"，其上次出现位置是头部，即第0位。

```csharp
// 构建好后缀
int[] buildGs(string p, out bool[] prefix) {
    prefix = new bool[p.Length];
    int[] suffix = new int[p.Length];
    for (int i = 0; i < p.Length; i++) {
        suffix[i] = -1;
    }
    int j = 0, k = 0;
    for (int i = 0; i < p.Length - 1; i++) {
        j = i;
        k = 0;
        while (j >= 0 && p[j] == p[p.Length - 1 - k]) {
            j--;
            k++;
            suffix[k] = j + 1;
        }
        if (j == -1) {
            prefix[k] = true;
        }
    }
    return suffix;
}
// 根据好后缀，获取模式串P的后移位数
int movebyGs(int bcIdx, int pLen, int[]suffix, bool[] prefix) {
    int dsLen = pLen - bcIdx - 1;
    if (suffix[dsLen] != -1) {
        return bcIdx - suffix[dsLen] - 1;
    }
    for (int i = bcIdx + 2; i < pLen; i++) {
        if (prefix[pLen - i]) {
            return i;
        }
    }
    return pLen;
}
```


## Sunday 算法
**算法思想：**
Sunday算法是一个线性字符串模式匹配算法。在匹配过程中，模式串并不被要求一定要按从左向右进行比较还是从右向左进行比较，它在发现不匹配时，算法能跳过尽可能多的字符以进行下一步的匹配，从而提高了匹配效率。 

Sunday算法的基本流程: 设字符串S，模式串为P，长度分别为n和m。首先对P进行预处理：记录P中每一种字符最后出现的位置，将其存入一个数组（假设为move）中。  

**算法实现**
Sunday算法是从前往后匹配，在匹配失败时关注的是主串中参加匹配的最末位字符的下一位字符。
  * 如果该字符没有在模式串中出现则直接跳过，即移动位数 = 模式串长度 + 1；
  * 否则，其移动位数 = 模式串长度 - 该字符最右出现的位置(以0开始) = 模式串中该字符最右出现的位置到尾部的距离 + 1。

文字描述过程：
  * 假设在发生不匹配时S[i]≠P[j]，1≤i≤n，1≤j≤m。设S此次第一个匹配的字符位置为l。显然，S[l+m]肯定要参加下一轮的匹配，并且P至少要与S[l+m]匹配才有可能与整个S匹配。
  * 这时我们就寻找P中S[l+m]出现的位置了。利用我们预处理好的数组move，可以O(1)算法复杂度查找出那个位置u，并将其直接移动至P[u]==S[l+m]。
  *  特殊地，若S[l+m]没有在P中出现，那么P不可能会与S[l+m]匹配，则将P的第一位直接移动到S[l+m+1]，继续匹配。直至l+m>n时，匹配完毕。

代码实现：
```csharp
// 构建move数组
Dictionary<char, int> buildMove(string p) {
    Dictionary<char, int> ret = new Dictionary<char, int>();
    for (int i = 0; i < p.Length; i++) {
        ret[p[i]] = p.Length - i;
    }
    return ret;
}
int sundy(string s, string p) {
    Dictionary<char, int> move = buildMove(p);
    int l = 0, j = 0; // S每次第一个匹配的字符位置，以及模式串已匹配了的长度
    while (l < s.Length - p.Length) {
        j = 0;
        while (s[l+j] == p[j]) {
            j++;
            if (j >= p.Length) {
                return l;
            }
        }
        if (move.ContainsKey(s[l+p.Length])) {
            l += move[s[l+p.Length]];
        } else {
            l += p.Length + 1;
        }

    }
    return -1;
}
```

**时间复杂度**
若模式串使得move中对应的值为1，即每次匹配失败时，只让模式串向后移动一位再进行匹配。这样会让Sunday算法的时间复杂度飙升到了O(m*n)，亦即字符串匹配的最坏情况。
