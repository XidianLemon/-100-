# 100大盗，是男人就做100道！
----------
## 目录
### 一、数组
1.二分查找

2.移除元素

3.有序数组的平方

4.长度最小的子数组

5.螺旋矩阵II

数组总结

### 二、链表
1.反转链表

2.k个一组反转链表

3.移除链表元素

4.两两交换链表中的节点

5.删除链表的倒数第N个节点

6.链表相交

7.环形链表

链表总结

### 三、哈希表
1.有效的字母异位词

2.两个数组的交集

3.快乐数

4.两数之和

5.四数相加 II

6.赎金信

7.三数之和

8.四数之和

哈希总结

### 四、字符串
1.反转字符串

2.反转字符串II

3.替换空格

4.翻转字符串里的单词

5.左旋转字符串

6.实现 strStr()

7.重复的子字符串

字符串总结

### 五、栈与队列
1.用栈实现队列

2.用队列实现栈

3.有效的括号

4.删除字符串中的所有相邻重复项

5.逆波兰表达式求值

6.滑动窗口最大值

7.前 K 个高频元素

栈与队列总结

### 六、双指针法
1.移除元素

2.反转字符串

----------

## 六、双指针法

## 2.反转字符串
双指针，一个指向开头，一个指向结尾，同时往中间靠，直到相遇，每次都swap。

```
class Solution {
public:
    void reverseString(vector<char>& s) {
        for (int i = 0, j = s.size() - 1; i < j; i++, j--) {
            swap(s[i], s[j]);
        }
    }
};
```

----------

## 1.移除元素
首先定义一个慢指针，指向0，快指针也是0开始，如果当前快指针的值等于给的值，则继续移动快指针，慢指针保持不动，如果当前快指针的值不等于给定的值，则将当前的快指针赋值给慢指针，然后慢指针往后移动1位。最后返回慢指针指向的那一位就可以了。

```
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int slow = 0;
        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] != val) {
                nums[slow] = nums[i];
                slow++;
            }
        }
        return slow;
    }
};
```

----------

## 五、栈与队列

## 栈与队列总结

面试题：栈里面的元素在内存中是连续分布的吗？

答：栈是容器适配器，底层容器使用不同的容器，导致栈内数据在内存中不是连续分布的。默认情况底层容器是deque，deque也是不连续的。

括号匹配，字符串去重，逆波兰表达式等匹配问题都是使用栈解决的经典问题。

队列的经典问题包括：滑窗最大值，前k个高频元素。其中滑窗最大值引出单调队列，前k个高频元素引出优先级队列，大顶堆小顶堆。

## 7.前 K 个高频元素
首先使用纯哈希的方法，优先级队列暂时我没看懂，等看懂后再用优先级队列。

方法1，纯哈希，首先将数组中的数放入哈希，first应当是数，second应当是频率。然后再将哈希放入一个二维数组中，注意该数组应当pair<int, int>，此时应当注意的是应该频率放到第一个，而数放到第二个。将该数组排序，那么排序的也就是频率。将频率排序完毕后，输出前k个，注意输出的是每一项的second。上述每一个注意都需要重点注意first和second！

```
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> myMap;
        vector<pair<int, int>> myVec;
        for (auto i : nums) {
            myMap[i] ++;
        }
        // myMap此时first存着是数，second存着是频率
        for (auto it = myMap.begin(); it != myMap.end(); it++) {
            myVec.push_back({it->second, it->first});
        }
        // myVec此时first存着是频率，second存的是数
        sort(myVec.rbegin(), myVec.rend());
        // myVec此时由大到小排序好了
        vector<int> result;
        for (int i = 0; i < k; i++) {
            result.push_back(myVec[i].second);
        }
        return result;
    }
};
```


方法2，优先级队列

优先级队列在这里就是用在了大顶堆和小顶堆上，大小顶堆的固定写法就是如下题中的，当push进去后，小顶堆固定的就是将小的排前面，大的放后面，而它可以当做队列来操作。本题首先实现一个小顶堆，然后将所有数的频率放到哈希表里。之后声明一个小顶堆，也就是优先级队列，以更小的数为优先。然后将哈希中的每一项都放入优先级队列中，当放入的时候，该优先级队列会自动的将放入的数以更小的优先放进去。那么当该优先级队列的size超过了k时，我就删除队头的元素，那么遍历完哈希表后，该优先级队列也就存下来了k个大的数。最后将该优先级队列的所有数都放到结果的vector中，就可以了，需要注意的就是放的时候需要先声明vector的长度，即`vector<int> result(k)`。然后放的时候也是result[i] = 队列.top().first。放完就删了队头元素。

```
class Solution {
public:
    class smallT {
    public:
        bool operator() (const pair<int, int>& lh, const pair<int, int>& rh) {
            return lh.second > rh.second;
        }
    };
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> myMap;
        for (int i = 0; i < nums.size(); i++) {
            myMap[nums[i]] ++;
        }
        priority_queue<pair<int, int>, vector<pair<int, int>>, smallT> myQue;
        for (unordered_map<int, int>::iterator it = myMap.begin(); it != myMap.end(); it++) {
            myQue.push(*it);
            if (myQue.size() > k) {
                myQue.pop();
            }
        }
        vector<int> result(k);
        for (int i = 0; i < k; i++) {
            result[i] = myQue.top().first;
            myQue.pop();
        }
        return result;
    }
};
```

----------

## 6.滑动窗口最大值

这道题按照暴力的方法好做，但是提交会超时。降低时间复杂度，就需要想策略。需要一个结构，该结构的头是最大的数，当我往该结构放的时候（也就是滑窗往右移动的时候），如果放的数比前面的数大，就把前面的数都删了，直到放的数比前面的小或者都删干净了为止。然后再放到该结构中。我要删除，也就是当滑窗往右移动的时候，删除也判断，删除的数是不是正好处在该结构的头上，不在该结构的头上，就不删除。因为他是目前已知最大的，而且不出滑窗，则不要删除。

先将前k个依次push到该结构中，然后将此时该结构的头放进结果。然后从第k个开始向后遍历，每一个都是先删除操作，删除的就是第i-k那一项，然后push操作，push就是第i项。然后将该结构的头放进结果中。
最后返回结果就可以了。

```
class Solution {
public:
    deque<int> myDeque;

    //删除的正好是头上那个，我才删
    void pop(int popNum) {
        if (!myDeque.empty() && myDeque.front() == popNum) {
            myDeque.pop_front();
        }
    }

    void push(int pushNum) {
        while (!myDeque.empty() && pushNum > myDeque.back()) {
            myDeque.pop_back();
        }
        myDeque.push_back(pushNum);
    }

    int front() {
        return myDeque.front();
    }

    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        for (int i = 0;i < k;i ++) {
            this->push(nums[i]);
        }
        vector<int> result;
        result.push_back(this->front());

        for (int i = k;i < nums.size();i ++) {
            this->pop(nums[i - k]);
            this->push(nums[i]);
            result.push_back(this->front());
        }
        return result;
    }
};
```

----------

## 5.逆波兰表达式求值

这道题最适合就是栈，给定的字符串，一项一项找，找到数字就放进栈，找到操作符就让栈顶的两个元素根据操作符相加，记得把栈顶元素删除，然后再把计算得到的数字再放回栈中。值得注意的是栈顶和栈顶第二个，哪个是放在操作符前，哪个是操作符后。然后减完记得转int，因为之前是字符转的。栈是int，返回也是int。最终直接返回栈顶的计算结果就行了。

```
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<int> st;
        for (int i = 0;i < tokens.size();i ++) {
            if (tokens[i] == "+" || tokens[i] == "-" || tokens[i] == "*" || tokens[i] == "/") {
                int num1 = st.top();
                st.pop();
                int num2 = st.top();
                st.pop();
                if (tokens[i] == "+") {
                    st.push(num2 + num1);
                }
                if (tokens[i] == "-") {
                    st.push(num2 - num1);
                }
                if (tokens[i] == "*") {
                    st.push(num2 * num1);
                }
                if (tokens[i] == "/") {
                    st.push(num2 / num1);
                }
            } else {
                st.push(stoi(tokens[i]));
            }
        }
        return st.top();
    }
};
```

----------

## 4.删除字符串中的所有相邻重复项

使用一个辅助字符串，判断原字符串的第i项 跟辅助串最后一项是否相同，相同就删了，不同就放进辅助串。最后返回辅助串就可以了。

```
class Solution {
public:
    string removeDuplicates(string s) {
        string st;
        for (char S : s) {
            if (S == st.back()) {
                st.pop_back();
            } else {
                st.push_back(S);
            }
        }
        return st;
    }
};
```

----------

## 3.有效的括号

首先用栈来存所有的右括号，只要我在遍历字符串的时候遇见了左括号，我就放一个右括号，对应括号的类型放。如果不是左括号呢，我就判断此时栈 是否为空，空的话就说明不是有效的，直接返回false。如果不是空则判断栈顶是否和字符串的这一位括号相同，相同则删除栈顶元素，不同则返回false。这样一圈遍历下来，如果此时栈还不是空的，则返回true。

```
class Solution {
public:
    bool isValid(string s) {
        stack<int> st;
        for (int i = 0;i < s.size();i ++) {
            if (s[i] == '(') {
                st.push(')');
            } else if (s[i] == '[') {
                st.push(']');
            } else if (s[i] == '{') {
                st.push('}');
            } else if (st.empty() || s[i] != st.top()) {
                return false;
            } else {
                st.pop();
            }
        }
        return st.empty();
    }
};
```

----------

## 2.用队列实现栈

这道题pop比较麻烦，别的都正常用就可以了。同样两个队列，pop实现过程：队列1的元素依次放到队列2中，留下最后一个，然后删除这个，再把队列2中所有的元素放到队列1中，清空队列2。有了思路就好写了。

```
class MyStack {
public:
    queue<int> que1;
    queue<int> que2;
    MyStack() {

    }
    
    void push(int x) {
        que1.push(x);
    }
    
    int pop() {
        int size = que1.size();
        while (size != 1) {
            que2.push(que1.front());
            que1.pop();
            size --;
        }
        int result = que1.front();
        que1.pop();
        while (!que2.empty()) {
            que1.push(que2.front());
            que2.pop();
        }
        return result;
    }
    
    int top() {
        return que1.back();
    }
    
    bool empty() {
        return que1.empty();
    }
};
```

----------

## 1.用栈实现队列


push就是push，删除的话首先判断out栈是否为空，不为空则直接删，为空则先将in栈所有的都依次放到out栈中，再删。peek取也是和pop一样的，只是记得删完再放回去。空则判断这两个栈是否都为空。


```
class MyQueue {
public:
    stack<int> stackOut;
    stack<int> stackIn;
    MyQueue() {

    }
    
    void push(int x) {
        stackIn.push(x);
    }
    
    int pop() {
        if (stackOut.empty()) {
            while (!stackIn.empty()) {
                stackOut.push(stackIn.top());
                stackIn.pop();
            }
        }
        int result = stackOut.top();
        stackOut.pop();
        return result;
    }
    
    int peek() {
        int result = this->pop();
        stackOut.push(result);
        return result;
    }
    
    bool empty() {
        return stackOut.empty() && stackIn.empty();
    }
};
```


----------

## 四、字符串

## 字符串总结

替换空格这类的题，也就是填充，可以先给数组扩容填充后的大小，然后从后往前操作。如果要求时间复杂度，慎用库函数，比如erase的时间复杂度是on。


## 7.重复的子字符串

kmp获取next数组，传入的字符串就是要判断的字符串。该字符串如果存在重复的子字符串，那么一定它的next数组一定会有这个性质：最后一位大于0，且字符串长度 % （字符串长度 - next数组最后一位） == 0，那么获得next数组后，就判断是否有这个性质，有就返回true。

```
class Solution {
public:
    void getNext(int* next, const string& s) {
        int j = 0;
        next[0] = 0;
        for (int i = 1; i < s.size(); i ++) {
            while (j > 0 && s[i] != s[j]) {
                j = next[j - 1];
            }
            if (s[i] == s[j]) {
                j++;
            }
            next[i] = j;
        }
    }
    bool repeatedSubstringPattern(string s) {
        if (s.size() == 0) {
            return false;
        }
        int next[s.size()];
        int len = s.size();
        getNext(next, s);
        if (next[len - 1] > 0 && (len % (len - next[len - 1]) == 0)) {
            return true;
        }
        return false;
    }
};
```

## 6.实现 strStr()

kmp首先获取next数组，然后两个串分别匹配，匹配不上就回退，回退多少就是看next，回退的循环条件是j>0且两个串一直不匹配。然后看下一个是否能匹配上，能的话j++，再判断此时j是否等于模式串的长度，等于就说明找到了，则返回当前项减去模式串长度再加1，就得到了结果。

    class Solution {
    public:
        void getNext(int* next, const string& s) {
            int j = 0;
            next[0] = 0;
            for (int i = 1; i < s.size(); i++) {
                while (j > 0 && s[i] != s[j]) {
                    j = next[j - 1];
                }
                if (s[i] == s[j]) {
                    j++;
                }
                next[i] = j;
            }
        }
        int strStr(string haystack, string needle) {
            if (needle.size() == 0) {
                return 0;
            }
            int j = 0;
            int next[needle.size()];
            getNext(next, needle);
            for (int i = 0; i < haystack.size(); i++) {
                while (j > 0 && haystack[i] != needle[j]) {
                    j = next[j-1];
                }
                if (haystack[i] == needle[j]) {
                    j ++;
                }
                if (j == needle.size()) {
                    return (i - needle.size() + 1);
                }
            }
            return -1;
        }
    };

## 5.左旋转字符串

首先翻转前n个，再翻转后面所有的，再整体翻转。这个技巧确实很厉害。

```
class Solution {
public:
    string reverseLeftWords(string s, int n) {
        reverse(s.begin(), s.begin() + n);
        reverse(s.begin() + n, s.end());
        reverse(s.begin(), s.end());
        return s;
    }
};
```

----------

## 4.翻转字符串里的单词

这道题有几个步骤，注意不要数组越界！这几个步骤感觉不用分顺序，去掉字符串中的重复空格；去掉头部的空格，去掉尾部空格，翻转整个串，翻转每个单词。使用api的时候要特别注意，reverse第一个参数是要翻转的第一位，第二个参数是要翻转的最后一位的下一位哦！！！erase第一个参数是从第几位开始翻转，第二个参数是翻转几位。最后当你删除了第I项的空格，需要记得I--，否则就会少删一位空格哦！

```
class Solution {
public:
    string reverseWords(string s) {

        //首先翻转整个字符串
        reverse(s.begin(), s.end());

        //将这个字符串中每个单词翻转
        for (int i = 0;i < s.length();i ++) {
            //找不是空格那一位
            while (i < s.length() && s[i] == ' ') {
                i ++;
            }
            //当i超过了数组长度时，则break
            if (i == s.length()) {
                break;
            }
            //此时i指向的是那个单词
            int wordStartIndex = i; //start指的是单词开头的索引
            

            //找单词结束的位置
            while (i < s.length() && s[i] != ' ') {
                i ++;
            }
            // if (i == s.length()) {
            //     i 
            // }
            int wordEndIndex = i;   //end指的是单词结束后，第一个空格的索引

            //翻转这个单词
            reverse(s.begin() + wordStartIndex, s.begin() + wordEndIndex);

        }

        //去掉重复空格
        for (int i = 0;i < s.length();i ++) {
            
            if (i > 0 && s[i] == s[i - 1] && s[i] == ' ') {
                s.erase(i - 1, 1);
                i --;
            }
        }

        //去掉开头处空格
        if (s[0] == ' ') {
            s.erase(0, 1);
        }

        //去掉结尾处空格
        if (s[s.length() - 1] == ' '){
            s.erase(s.length() - 1, 1);
        }
        return s;
    }
};
```

----------


## 3.替换空格


首先数空格的个数，然后声明一个新的字符串，给该字符串定长度：原字符串长度+空格个数乘以2。从后往前遍历，若遇到空格则将新字符串的三位替换为%20，不遇到空格则一直将原字符串的字符放到新字符串中。长度最好用length()。

```
class Solution {
public:
    string replaceSpace(string s) {
        string resStr;
        int countSpace = 0;
        for (int i = 0;i < s.length();i ++) {
            if (s[i] == ' ') {
                countSpace ++;
            }
        }
        resStr.resize(countSpace * 2 + s.length());
        int sSize = s.length() - 1;
        int resSize = resStr.length() - 1;
        for (int i = sSize, j = resSize;i >= 0;i --, j --) {
            if (s[i] == ' ') {
                resStr[j] = '0';
                resStr[j - 1] = '2';
                resStr[j - 2] = '%';
                j = j - 2;
            } else {
                resStr[j] = s[i];
            }
        }
        return resStr;
    }
};

```


----------


## 2.反转字符串II


这题的题目表达很差，转换成好理解的话，就是每隔k个，就反转k个，不足k个就都翻转。知道了这个就简单了，每隔k个，那就是for循环的时候+2k，在for循环中判断当前项是否不足k个了，不足k个就当前到最后都翻转，多于k个就翻转k个。

```
class Solution {
public:
    string reverseStr(string s, int k) {
        for (int i = 0;i < s.length();i = i + 2*k) {
            if (s.length() - i < k) {
                reverse(s.begin() + i, s.end());
            } else {
                reverse(s.begin() + i, s.begin() + i + k);
            }
        }
        return s;
    }
};
```

----------

## 1.反转字符串


双指针比较好，也不用引入额外空间，头尾交换，一起收缩，到中间就停。

```
class Solution {
public:
    void reverseString(vector<char>& s) {
        for (int i = 0, j = s.size() - 1;i < s.size() / 2;i ++, j --) {
            swap(s[i], s[j]);
        }
    }
};
```

----------


## 三、哈希表

## 哈希总结

哈希分为三种：1.数组；2.set；3.map。

1.数组。数组适用于包含小写字母，小写字母有26个，每一个对应数组的一位，每遇见一位，就在对应的位置加1。

2.set。没有限制字符的大小的情况，就不能用数组做哈希了。这是由于数组大小是有限的，受到系统栈空间的限制。而如果数组空间足够大，但哈希值比较少，特别分散，跨度特别大，使用数组就造成空间的极大浪费。那么此时做映射就应当用set。

3.map。数组大小收到限制，如果元素很少，但哈希值太大会造成内存空间浪费。set是一个集合，里面放的元素只能是一个key。那么使用set就只能判断是否存在，但不能记录下标。map是一种键值对的结构，那么使用key保存数值，使用value保存数值所在的下标。


## 8.四数之和


这道题和三数之和类似，值得注意的点在于本题的四数之和是一个随机数，而三数之和是0，因此在每次循环开始的地方就不能判断如果超过target就返回了。三数之和只需要定一个数，左右两个数，四数之和定两个数，因此就需要两层for循环，并且每层for循环在最开始的地方去重。其他的就和三数之和一样了，当左小于右，就一直往中间找，看是大了还是小了，就往中间缩，直到左大于等于右。还有需要注意的是四数之和与target比较大小时，不等号左边就保留两个数的和，右边是target - 另外两个数的和，否则就会出现四数之和超过了int的最大值，执行出错！
                                                     
```
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        vector<vector<int>> result;
        sort(nums.begin(), nums.end());
        for (int i = 0; i < nums.size(); i ++) {
            if (i > 0 && nums[i] == nums[i-1]) {
                continue;
            }
            for (int j = i+1; j < nums.size(); j ++) {
                if (j > i+1 && nums[j] == nums[j-1]) {
                    continue;
                }
                int left = j + 1;
                int right = nums.size() - 1;

                while (left < right) {
                    if (nums[i] + nums[j] < target - nums[left] - nums[right]) {
                        left ++;
                    } else if (nums[i] + nums[j] > target - nums[left] - nums[right]) {
                        right --;
                    } else {
                        result.push_back({nums[i], nums[j], nums[left], nums[right]});
                        while (left < right && nums[left] == nums[left + 1]) {
                            left++;
                        }
                        while (left < right && nums[right] == nums[right - 1]) {
                            right--;
                        }
                        left ++;
                        right --;
                    }
                }
            }
        }
        return result;
    }
};
```


----------


## 7.三数之和


不想用哈希做容易错还不好理解，就用快慢指针就可以了，首先排序，然后在最外的for循环中去重。相当于固定其中一个，找剩下两数，这两数先从两头找，当找到后一起去重，去重完毕就收缩。

```
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        sort(nums.begin(), nums.end());
        for (int i = 0;i < nums.size();i ++) {
            if (nums[i] > 0) {
                return res;
            }
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            int left = i + 1;
            int right = nums.size() - 1;
            while(left < right) {
                if (nums[i] + nums[left] + nums[right] > 0) {
                    right --;
                } else if (nums[i] + nums[left] + nums[right]) {
                    left ++;
                } else {
                    res.push_back(vector<int>{nums[i], nums[left], nums[right]});
                    while (left < right && nums[left] == nums[left + 1]) {
                        left ++;
                    }
                    while (left < right && nums[right] == nums[right - 1]) {
                        right --;
                    }
                    left ++;
                    right --;
                }
            }
        }
        return res;
    }
};
```

----------

## 6.赎金信


这道题跟第一题类似，同样是将每一个字符和'a'相减，得到的数字放到一个int数组中，注意数组长度是26，要在初始化时候指定！将要从中获取的那个string中所有的都放到数组中，放的时候就++，然后取，因为不能重复使用，因此取了就--，如果这一位小于0，说明要从中获取的数组不够，那就直接返回false。

```
class Solution {
public:
    bool canConstruct(string ransomNote, string magazine) {
        int arr[26] = {0};
        for (int i = 0;i < magazine.size();i ++) {
            arr[magazine[i] - 'a'] ++;
        }
        for (int i = 0;i < ransomNote.size();i ++){
            arr[ransomNote[i] - 'a'] --;
            if(arr[ransomNote[i] - 'a'] < 0){
                return false;
            }
        }
        return true;
    }
};
```

----------

## 5.四数相加 II


首先遍历前两个数，将前两个数字的和放入一个unordered_map中，注意！由于unordered_map不可重复，因此两数的和重复的那一位就会+1！！！然后再遍历后两个数，在这个unordered_map中查找0 - (c+d)，注意此时查找，找的是key，而这个值对应的是有几个这个值，因此需要将这一位的值累加上去，比如unordered_map umap，umap[i]对应的值就是有几个这个数。因此要将这个值累加！

```
class Solution {
public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        unordered_map<int, int> umap;
        for (int a : nums1) {
            for (int b : nums2) {
                umap[a + b] ++;
            }
        }
        int count = 0;
        for (int c : nums3) {
            for (int d : nums4) {
                if(umap.find(0 - (c + d)) != umap.end()){
                    count += umap[0 - (c + d)];
                }
            }
        }
        return count;
    }
};
```

----------

## 4.两数之和


这道题在两年前居然做过，使用的暴力解法，就是两个for循环，没啥意思。卡尔老师讲的使用unordered_map很巧妙。使用target减去当前的数，看是否在unordered_map中出现过，如果出现过则直接返回当前下标和那一项的key。如果没出现过则将当前的下标和数都放进unordered_map中。值得注意的就是使用了auto来接find返回的值，并且用这个auto的对象的second来访问这一项的key。这里是比较陌生的，如果想访问这一项的值就是first。

```
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> numMap;
        for (int i = 0; i < nums.size(); i ++) {
            auto iter = numMap.find(target - nums[i]);
            if (iter != numMap.end()){
                return {i, iter->second};
            }
            numMap.insert(pair<int, int>(nums[i], i));
        }
        return {};
    }
};
```

----------

## 3.快乐数


这道题也要用哈希，原因是如果该数之和一直不为1，则一定会有一个循环的、不为1的数作为所有位相加的和，而只要有和之前一样的数出现了，就不是快乐数。那么使用unordered_set来存和找是否有一样的数。解题的重点部分就在分解这个整数，首先从个位，该数对10取余则得到了个位数，将个位数求平方。然后该数除以10，得到的是不包含个位数的，再对10取余，得到此时的个位数再平方，一直循环到除以10得到0，就将该数每一位都平方后相加了。看得到的数是不是1。然后将该数在unordered_set中找，有的话则说明陷入循环就返回false，没有则insert进去。

```
class Solution {
public:
    int getSum(int n) {
        int resSum = 0;
        while (n != 0) {
            resSum += (n % 10) * (n % 10);
            n = n / 10;
        }
        return resSum;
    }
    bool isHappy(int n) {
        unordered_set<int> resNumSet;
        while (1) {
            int numSum = getSum(n);
            if (numSum == 1) {
                return true;
            }
            if (resNumSet.find(numSum) != resNumSet.end()) {
                return false;
            } else {
                resNumSet.insert(numSum);
            }
            n = numSum;
        }
    }
};
```

----------

## 2.两个数组的交集


使用的是unordered_set，查找这个数字是否在另一个数组出现过，查找是否出现过就用这个stl。先使用一个unordered_set，存第一个数组，遍历第二个数组的每一项，看是否在前面的unordered_set中出现过这个数，出现过的就把这个数放到另一个unordered_set中，注意unordered_set中的元素是不能重复的！这和题目要求输出的是一致的，最终使用vector来存这个unordered_set。

```
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> result;
        unordered_set<int> temp(nums1.begin(), nums1.end());
        for(int num : nums2){
            if(temp.find(num) != temp.end()){
                result.insert(num);
            }
        }
        vector<int> resultVec(result.begin(), result.end());
        return resultVec;
    }
};
```

----------

## 1.有效的字母异位词


哈希表，用数组就可以了。数组记录每个字母出现的次数，那么数组长度就是26个。比如'a'出现第一次，那么数组的第0项就是1，之后再出现就再加1。比较的话就是s[i] - 'a'，加入s[i]是b，相减就是1。那么此时将数组的第1项+1，字符串1加上去一个，字符串2每次遇到就减去这一项，直到size结束，看此时数组里有没有不是0的，有的话一定不是异位词。

```
class Solution {
public:
    bool isAnagram(string s, string t) {
        int record[26] = {0};
        for (int i = 0;i < s.size();i ++){
            record[s[i] - 'a']++;
        }
        for (int i = 0;i < t.size();i ++){
            record[t[i] - 'a']--;
        }
        for (int i = 0;i < 26;i ++) {
            if (record[i] != 0){
                return false;
            }
        }
        return true;
    }
};
```

----------


## 二、链表


## 链表总结


几个技巧，虚拟头结点，快慢指针等。反转链表多做了前半部分和一部分的反转，都用的递归，好难，暂时不做那个了。删除链表的话，一定记着先将后驱记录，再删除 ，否则之后就找不到了。


## 7.环形链表


方法一：哈希表，这题用哈希表最方便，也最好理解。哈希表里存着每一个节点，当找了一圈之后，第一个重复的节点就是环的入口。方法二暂时不准备做，以后再补。
```
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        unordered_set<ListNode*> uds;
        while(head != NULL){
            if(uds.count(head)){
                return head;
            }
            uds.insert(head);
            head = head->next;
        }
        return NULL;
    }
};
```

----------

## 6.链表相交


首先计算两个链表的长度，看谁长，长多少，长多少就让长的那个先走几步。这里为了省事就swap了一下，统一让heada长。然后虚拟头结点分别指向两个链表头结点。长的那个先走几步，到了之后就一起走。直到两个节点的next相等或者其中一个走到null就停。while循环结束后，说明下一个就是相交的节点，直接返回下一个就可以了。
```
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        int lengthA = 0;
        int lengthB = 0;
        ListNode* tempA = new ListNode(0);
        tempA->next = headA;
        ListNode* tempB = new ListNode(0);
        tempB->next = headB;
        while (tempA->next != NULL) {
            tempA = tempA->next;
            lengthA ++;
        }
        while (tempB->next != NULL) {
            tempB = tempB->next;
            lengthB ++;
        }
        
        //此时(lengthB - lengthA)存的是两个链表之间相差几个
        //这时应当看谁长，谁长我就让谁先走几步，我统一让第一个链表是长的，
        if(lengthA < lengthB){
            swap(lengthA, lengthB);
            swap(headA, headB);
        }
        int diffLengthAB = lengthA - lengthB;
        ListNode* dummyHeadA = new ListNode(0);
        dummyHeadA->next = headA;
        ListNode* dummyHeadB = new ListNode(0);
        dummyHeadB->next = headB;
        //虚拟头结点，让第一个链表先走(lengthB - lengthA)
        while(diffLengthAB != 0){
            dummyHeadA = dummyHeadA->next;
            diffLengthAB --;
        }
        //两链表同时开始走
        while(dummyHeadA->next != dummyHeadB->next && dummyHeadA->next != NULL && dummyHeadB->next != NULL){
            dummyHeadA = dummyHeadA->next;
            dummyHeadB = dummyHeadB->next;
        }
        return dummyHeadB->next;
    }
};
```

----------

## 5.删除链表的倒数第N个节点


快慢指针，虚拟头结点。首先快慢都在dummy那，然后让快指针走n个，然后快慢同时走，直到快的的next为null就停止删除此时slow的下一个，令slow指向下下个即完成。
```
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* dummyHead = new ListNode(0);
        dummyHead->next = head;
        ListNode* fast = dummyHead;
        ListNode* slow = dummyHead;
        while(n != 0){
            fast = fast->next;
            n--;
        }
        while(fast->next != NULL){
            fast = fast->next;
            slow = slow->next;
        }
        ListNode* tmp = slow->next;
        slow->next = slow->next->next;
        delete tmp;
        return dummyHead->next;
    }
};
```

----------

## 4.两两交换链表中的节点


首先画出图，链表问题必须画图，虚拟头结点必须的。将后面几个节点存下来，我这里就是不知道存谁那就都存，存下来后开始交换，按照cur的next先放，next的next后放，next的next的next最后，则依次将现在的都第一遍前两个都放完了就进行下次循环，直到结束。
```
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        ListNode* dummyHead = new ListNode(0);
        dummyHead->next = head;
        ListNode* cur = dummyHead;
        while(cur->next != nullptr && cur->next->next != nullptr){
            ListNode* curNxt = cur->next;
            ListNode* curNxtNxt = cur->next->next;
            ListNode* curNxtNxtNxt = cur->next->next->next;
            cur->next = curNxtNxt;
            cur->next->next = curNxt;
            cur->next->next->next = curNxtNxtNxt;
            cur = cur->next->next;
        }
        return dummyHead->next;
    }
};
```

----------

## 3.移除链表元素


虚拟头结点应该是一个更好的方法：首先虚拟一个头结点，它得next为head，也就是它指向head，然后当前节点cur不要是head，而是head之前的那一节点也就是dummy，这样的话它就可以判断头结点的val了，而不需要额外添加逻辑判断。之后就一直遍历当前节点cur，只要cur的下一个不为val，则往后找，下一个是val则删除再指向之后的。这期间虚拟节点始终都在head的前一节点，虽然head节点删除了，但是整条链表没断，因此虚拟节点此时的next就是没删除的第一项，将其赋值给head，然后返回head，就返回了整条删除val后的链表。

```
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        ListNode* dummyHead = new ListNode(0);
        dummyHead->next = head;
        ListNode* cur = dummyHead;
        while(cur->next != NULL){
            if(cur->next->val == val){
                ListNode* tmp = cur->next;
                cur->next = cur->next->next;
                delete tmp;
            }else{
                cur = cur->next;
            }
        }
        head = dummyHead->next;
        delete dummyHead;
        return head;
    }
};
```


第一点就是c++需要手动delete节点，就是当移除了指定的链表元素后，delete它。第二点要注意就是特殊情况，整个一个链表都是指定的值或者从头开始是指定的值，那么应当注意的是首先一个while，就是让head指向第一个不为给定值得地方（前面删了的元素记得delete）；然后一个while循环,条件是当前节点和下一节点都不为空则进入，如果下一个节点和指定值不同，则当前节点往后移，如果下一个节点和指定值相同，则删了下一个节点，指向下下个，当前还是在原地不能动。
```
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        while(head != NULL && head->val == val){
            ListNode* tmp = head;
            head = head->next;
            delete tmp;
        }
        ListNode* cur = head;
        while(cur != NULL && cur->next != NULL){
            if(cur->next->val == val){
                ListNode* tmp = cur->next;
                cur->next = cur->next->next;
                delete tmp;
            }else{
                cur = cur->next;
            }
        }
        return head;
    }
};
```


----------

## 2.k个一组反转链表


这道题首先要想到的是给你两个链表中的节点，让你反转中间这些，比如从a到b反转，那么要注意应当反转的是a到b-1，b不参与反转，因为b就相当于下次的a。

第一个递归函数就是反转从a到a-1，转过来返回的是pre节点，改节点指的是b-1的节点。

第二个递归函数就是k个一组转了，那么首先将b节点转到第k+1个位置，然后调用上面第一个递归输入就是此时的a和b。这是k个一组其中的一组。然后再调用当前递归函数，输入就是b和k，也就是下一组和k个，而返回值应当令a->next指向它即转之后再和下一组转好的连接上。最后返回第一次掉上面第一个递归那个newhead。

```
class Solution {
public:
    ListNode* reverse(ListNode* a, ListNode* b){
        ListNode* pre = NULL;
        ListNode* cur = a;
        ListNode* nxt = a;
        while(cur != b){
            nxt = cur->next;
            cur->next = pre;
            pre = cur;
            cur = nxt;
        }
        return pre;
    }
    ListNode* reverseKGroup(ListNode* head, int k) {
        if(head == NULL){
            return NULL;
        }
        ListNode* a = head;
        ListNode* b = head;
        for(int i = 0;i < k;i ++){
            if(b == NULL){
                return head;
            }
            b = b->next;
        }
        ListNode* newHead = reverse(a, b);
        a->next = reverseKGroup(b, k);
        return newHead;
    }
};
```


----------


## 1.反转链表


反转链表目前只做迭代法，不做递归，这种计算机思想暂时不好理解，跳过。

记得皮城执法官说过的：如果碰壁了，就用力把它碰开。跳过是不可能跳过的，这辈子不可能跳过的。

递归反转它来了！
首先是全部反转，一上来先判断head是否为null，不这样运行不过哦。然后判断head->next是否为null，这是递归的basecase，如果head的下一个为空则返回head本身。接着将除了头的都反转。最后将头反转。

```
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(head == NULL){
            return NULL;
        }
        if(head->next == NULL){
            return head;
        }
        ListNode* res = reverseList(head->next);
        head->next->next = head;
        head->next = NULL;
        return res;
    }
};
```

再然后是前一部分反转，这部分同样一上来判断head是否为null，然后希望记录下来转之后的后继节点使用nxt记录，当转n-1次后，也就是basecase是转了n-1次则返回此时的头，但是同时必须要记录下来的是后继。接着递归转n-1次，然后再把头反转，同时头的next应当指向后继。


```
    ListNode* nxt = NULL;
    ListNode* reverseN(ListNode* head, int n){
        if(head == NULL){
            return NULL;
        }
        if(n == 1){
            nxt = head->next;
            return head;
        }
        ListNode* last = reverseN(head->next, n - 1);
        head->next->next = head;
        head->next = nxt;
        return last;
    }
```


最后就是部分反转，部分反转的basecase，就是当left等于1的时候，相当于是前一部分反转，那么此时将头到right都反转，返回的是翻转后的头结点。接着做部分反转的递归，递归后，应当让头的next指向翻转后的头结点。此时已经不用管后继是否街上了因为在前一部分反转时，已经接上了。最后返回头结点就可以了！
head的next指向反转


```
class Solution {
public:
    ListNode* nxt = NULL;
    ListNode* reverseN(ListNode* head, int n){
        if(head == NULL){
            return NULL;
        }
        if(n == 1){
            nxt = head->next;
            return head;
        }
        ListNode* last = reverseN(head->next, n - 1);
        head->next->next = head;
        head->next = nxt;
        return last;
    }
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        if(head == NULL){
            return NULL;
        }
        if(left == 1){
            return reverseN(head, right);
        }
        head->next = reverseBetween(head->next, left - 1, right - 1);
        return head;
    }
};
```


反转链表就从头开始转，首先将head和head.next打开，打开后，就找不到next了，那么就需要用一个变量存head.next。存下来后，就可以放心的将head.next赋值为前一个节点了。前一个节点就需要新建一个变量存。head.next赋值为前一个节点这个操作就是反转了。反转后就令当前的和前一个都后移，就是将当前的赋值前一个，之前存的head.next赋值给当前的，就完成了一起后移。


    class Solution {
    public:
        ListNode* reverseList(ListNode* head) {
            ListNode* pre = NULL;
            ListNode* temp;
            while(head){
                temp = head->next;
                head->next = pre;
                pre = head;
                head = temp;
            }
            return pre;
        }
    };


部分反转，此题为92. 反转链表 II，这道题按照下面的方法，先让pre移动到要反转区间的前驱，cur移动到反转区间第一个，cur和第三项相连，第二项和前驱相连，这样就让cur变到了第二位。cur再和下下位相连，下一位在和pre相连。也就是每一次将后面一项提到前面，前面往后移。反转次数就是right - left。


```
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        ListNode* dummy = new ListNode(-1);
        dummy->next = head;
        ListNode* pre = dummy;
        for(int i = 0;i < left - 1;i++){
            pre = pre->next;
        }
        ListNode* cur = pre->next;
        for(int j = left;j < right;j++){
            ListNode* t = cur->next;
            cur->next = t->next;
            t->next = pre->next;
            pre->next = t;
        }
        return dummy->next;
    }

};
```

----------

## 一、数组

## 数组总结
数组是存放在连续内存空间上的相同类型数据的集合。
数组的元素是不能删的，只能覆盖。
数组下标都是从0开始的。
数组内存空间的地址是连续的。
二维数组在内存的空间地址，不是m*n连续，而是m条连续的地址空间。
经典题目二分查找，快慢指针，滑动窗口，模拟行为。

## 5.螺旋矩阵II
首先注意二维数组初始化是这样哦！

    vector<vector<int>> res(n, vector<int>(n, 0));  //二维数组初始化

这道题主要是看规律，给的n如果是奇数，则中间会有一项，如果是偶数，中间没有这一项，则在最开始判断是奇偶，奇数则给中间的数赋值。由于是顺时针给数组赋值，转一圈就是一次循环，则看要转几圈。这个圈数则是作为循环的次数。而在每次循环中，要分为四种方向上下左右。每种方向都需要对该方向的每一个元素赋值，则该方向本身要作为一个循环。那么每圈四个循环，循环结束条件是当前的x坐标或者y坐标加到了这一行或这一列的倒数第二项尤其注意这个循环条件！在第四个循环结束时，应当将当前指针指向下次循环开始的那一项，且圈数加1，表示转到第几圈了。总共有n/2圈，如果超过了n/2圈则退出循环，返回，结束。

    class Solution {
    public:
        vector<vector<int>> generateMatrix(int n) {
            vector<vector<int>> res(n, vector<int>(n, 0));  //二维数组初始化
            // int cycle = n / 2;  //看总共需要绕几圈
            int cycletemp = 1;  //绕到了第几圈
            int corX = 0;
            int corY = 0;       //这两个是放的那个坐标
            int num = 1;        //指每次放的那个数
    
            if(n % 2 == 1){
                res[n / 2][n / 2] = n * n;
            }
            while(cycletemp <= n / 2){
                while(corY < n - cycletemp){
                    res[corX][corY] = num;
                    corY = corY + 1;
                    num = num + 1;
                }
                while(corX < n - cycletemp){
                    res[corX][corY] = num;
                    corX = corX + 1;
                    num = num + 1;
                }
                while(corY > cycletemp - 1){
                    res[corX][corY] = num;
                    corY = corY - 1;
                    num = num + 1;
                }
                while(corX > cycletemp - 1){
                    res[corX][corY] = num;
                    corX = corX - 1;
                    num = num + 1;
                }
                corX = corX + 1;
                corY = corY + 1;    //移动到下一个圈的起点
                cycletemp = cycletemp + 1;  //下一圈开始
            }
            return res;
        }
    };


----------
## 4.长度最小的子数组
### 暴力解法：
太耗时了，思路大概是这样的：我要有一个中间变量存当前的子数组长度，还有一个变量存最小的那个长度。然后两个for循环之间呢，要注意将中间变量长度置0，而且求和的那个sum也要在此置零。再一个就是刚开始比较，也就是第一次累和大于target的时候，需要判断res是否为0，为0则将中间变量赋值给res，作为初始的那个来比较。

    class Solution {
    public:
        int minSubArrayLen(int target, vector<int>& nums) {
            int res = 0;
            int sum = 0;
            int restemp = 0;
            for(int i = 0;i < nums.size();i++){
                sum = 0;
                restemp = 0;
                for(int j = i;j < nums.size();j++){
                    sum = sum + nums[j];
                    if(sum >= target){
                        restemp = j - i + 1;
                        if(res > restemp || res == 0){
                            res = restemp;
                        }
                        break;
                    }
                }
            }
            return res;
        }
    };

### 滑窗（双指针）解法：
同样要设置中间变量存当前长度最小的，思路就是两个指针，i和j，i一直往前动，直到比target大了，就停下！记录下当前长度，然后将j指针往前移动一位，再判断是否比target大，还大则接着记录当前target，如果不大了呢，就令i往前移动一个，接着判断是否比target大。同样的，也应当注意第一次的也就是刚开始比较，第一次累和大于target的时候，需要判断res是否为0，为0则将中间变量赋值给res，作为初始的那个来比较。

    class Solution {
    public:
        int minSubArrayLen(int target, vector<int>& nums) {
            int sum = 0;
            int res = 0;
            int restemp = 0;
            int j = 0;
            for(int i = 0;i < nums.size();i ++){
                sum = sum + nums[i];
                while(sum >= target){
                    restemp = i - j + 1;
                    if(res > restemp || res == 0){
                        res = restemp;
                    }
                    sum = sum - nums[j];
                    j++;
                }
            }
            return res;
        }
    };

----------

## 3.有序数组的平方

双指针法，或者暴力解法，暴力解法没什么可写的，双指针法的话，一定要注意声明数组时候，要给他初始化一个大小，否则运行时会出错！

    class Solution {
    public:
        vector<int> sortedSquares(vector<int>& nums) {
            vector<int> res(nums.size());
            int left = 0;
            int right = nums.size() - 1;
            // cout<<"right = "<<right<<endl;
            for(int i = nums.size() - 1;i >= 0;i --){
                //cout<<"nums[left] = "<<nums[left]<<endl;
                //cout<<"nums[right] = "<<nums[right]<<endl;
                if((nums[left] * nums[left]) >= (nums[right] * nums[right])){
                    res[i] = nums[left] * nums[left];
                    // cout<<"res[i]"<<res[i]<<endl;
                    left ++;
                }else{
                    // cout<<"nums[right] = "<<nums[right]<<endl;
                    res[i] = nums[right] * nums[right];
                    // cout<<"res[i]"<<res[i]<<endl;
                    right --;
                }
            }
            return res;
        }
    };

----------
## 2.移除元素
暴力解决的方法着重注意的地方就是当集体前移之后，要注意size--，否则后面都是相同的val，不好处理。尤其注意的是for循环，应当小于size，而不是nums.size()!!!!!!!
双指针法重点是思路，看快指针指的元素是否等于val，等于的话，则慢指针不动，快指针后移；不等的话，快指针的值赋值给慢指针，俩人一起后移。
### 暴力解法
    class Solution {
    public:
        int removeElement(vector<int>& nums, int val) {
            int size = nums.size();
            for(int i = 0;i < size;i++){
                if(nums[i] == val){
                    for(int j = i;j < size-1;j++){
                        nums[j] = nums[j+1];
                    }
                    i--;
                    size --;
                }
            }
            return size;
        }
    };
### 双指针
    class Solution {
    public:
        int removeElement(vector<int>& nums, int val) {
            int slow = 0;
            for(int fast = 0;fast < nums.size();fast++)
                if(nums[fast] != val)
                    nums[slow++] = nums[fast];
            
            return slow;
        }
    };

----------

## 1.二分查找

二分查找前提是数组有序，无重复元素。
难点是边界条件，区间的定义要想清楚，区间的定义就是不变量，要在二分查找过程中保持不变。
左闭右闭，那么边界条件就是left==right,right初始应当取size-1。

    class Solution {
    public:
        int search(vector<int>& nums, int target) {
            int left = 0;
            int right = nums.size() - 1;
            int middle;
            while(left <= right){
                middle = left + (right - left) / 2;
                if(nums[middle] < target){
                    left = middle + 1;
                }else if(nums[middle] > target){
                    right = middle - 1;
                }else{
                    return middle;
                }
            }
            return -1;
        }
    };




