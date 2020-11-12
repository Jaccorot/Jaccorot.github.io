---
	layout: post
title:  "leetcode book"
date:   2020-11-02 11:00:00 +0800
categories: python leetcode
tags: python leetcode argrithms
excerpt: leetcode leetbook
---

* content
{:toc}


# 初级算法

## 数组

[链接](https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x2i30g/)

### 删除排序数组中的重复项

给定一个排序数组，你需要在 原地 删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。

 

> 示例 1:
>
> 给定数组 nums = [1,1,2], 
>
> 函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。 
>
> 你不需要考虑数组中超出新长度后面的元素。
> 示例 2:
>
> 给定 nums = [0,0,1,1,1,2,2,3,3,4],
>
> 函数应该返回新的长度 5, 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4。
>
> 你不需要考虑数组中超出新长度后面的元素。
>
>
> 说明:
>
> 为什么返回数值是整数，但输出的答案是数组呢?
>
> 请注意，输入数组是以「引用」方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。
>
> 你可以想象内部操作如下:
>
> // nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
> int len = removeDuplicates(nums);
>
> // 在函数里修改输入数组对于调用者是可见的。
> // 根据你的函数返回的长度, 它会打印出数组中该长度范围内的所有元素。
> for (int i = 0; i < len; i++) {
>     print(nums[i]);
> }

```python
# 快慢指针
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        i = 0
        for j in range(1,len(nums)):
            if nums[i] != nums[j]:
                nums[i+1] = nums[j]
                i += 1
        return i+1
# 笨方法：set -》 list -》 sort
    def removeDuplicates(self, nums: List[int]) -> int:
        nums[:] = set(nums)
      	nums.sort()
        return len(nums)
```

### 买卖股票的最佳时机 II

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

 

> 示例 1:
>
> 输入: [7,1,5,3,6,4]
> 输出: 7
> 解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
>      随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
> 示例 2:
>
> 输入: [1,2,3,4,5]
> 输出: 4
> 解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
>      注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。
>      因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
> 示例 3:
>
> 输入: [7,6,4,3,1]
> 输出: 0
> 解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
>
>
> 提示：
>
> 1 <= prices.length <= 3 * 10 ^ 4
> 0 <= prices[i] <= 10 ^ 4
>

```python
# 假设每天都买入，每天都卖出，计算两天差价是正值就加入最大利润，负值就跳过
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        max_profits = 0
        for i in range(len(prices)-1):
            if prices[i+1] - prices[i]>0:
                max_profits +=prices[i+1] - prices[i]
        return max_profits

```

### 旋转数组

给定一个数组，将数组中的元素向右移动 k 个位置，其中 k 是非负数。

> 示例 1:
>
> 输入: [1,2,3,4,5,6,7] 和 k = 3
> 输出: [5,6,7,1,2,3,4]
> 解释:
> 向右旋转 1 步: [7,1,2,3,4,5,6]
> 向右旋转 2 步: [6,7,1,2,3,4,5]
> 向右旋转 3 步: [5,6,7,1,2,3,4]
> 示例 2:
>
> 输入: [-1,-100,3,99] 和 k = 2
> 输出: [3,99,-1,-100]
> 解释: 
> 向右旋转 1 步: [99,-1,-100,3]
> 向右旋转 2 步: [3,99,-1,-100]
> 说明:
>
> 尽可能想出更多的解决方案，至少有三种不同的方法可以解决这个问题。
> 要求使用空间复杂度为 O(1) 的 原地 算法。

```python
class Solution:
    def rotate(self, nums: List[int], k: int) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        nums_len = len(nums)
        
        # 列表拼接法
        # nums[:] = nums[nums_len-k:] + nums[:nums_len-k]

        # 迭代pop，insert法
        # while k:
        #     nums.insert(0,nums.pop())
        #     k -= 1
        
        #循环构建新数组替换法
        new_list = []
        for i in range(nums_len):
            target = (i + nums_len - k) % nums_len
            new_list.append(nums[target])
        nums[:] = new_list

```

### 存在重复元素

给定一个整数数组，判断是否存在重复元素。

如果任意一值在数组中出现至少两次，函数返回 true 。如果数组中每个元素都不相同，则返回 false 。

 

> 示例 1:
>
> 输入: [1,2,3,1]
> 输出: true
> 示例 2:
>
> 输入: [1,2,3,4]
> 输出: false
> 示例 3:
>
> 输入: [1,1,1,3,3,4,3,2,4,2]
> 输出: true
>

```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        return len(nums) != len(set(nums))
```

### 只出现一次的数字

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

> 说明：
>
> 你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？
>
> 示例 1:
>
> 输入: [2,2,1]
> 输出: 1
> 示例 2:
>
> 输入: [4,1,2,1,2]
> 输出: 4

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        # Counter 计数法
        # from collections import Counter
        # s = Counter(nums)
        # return s.most_common()[-1][0]

        # 先排序，后比较
        # nums.sort()
        # for i in range(0,len(nums)-1,2):
        #     if nums[i] != nums[i+1]:
        #         return nums[i]

        # return nums[-1]

        # 异或
        return reduce(lambda x,y: x ^ y , nums)
```

### 两个数组的交集 II

给定两个数组，编写一个函数来计算它们的交集。

 

> 示例 1：
>
> 输入：nums1 = [1,2,2,1], nums2 = [2,2]
> 输出：[2,2]
> 示例 2:
>
> 输入：nums1 = [4,9,5], nums2 = [9,4,9,8,4]
> 输出：[4,9]
>
>
> 说明：
>
> 输出结果中每个元素出现的次数，应与元素在两个数组中出现次数的最小值一致。
> 我们可以不考虑输出结果的顺序。
> 进阶：
>
> 如果给定的数组已经排好序呢？你将如何优化你的算法？
> 如果 nums1 的大小比 nums2 小很多，哪种方法更优？
> 如果 nums2 的元素存储在磁盘上，内存是有限的，并且你不能一次加载所有的元素到内存中，你该怎么办？

```python
class Solution:
    def intersect(self, nums1: List[int], nums2: List[int]) -> List[int]:
        # 1. 先排序，后双指针比较
        # nums1.sort()
        # nums2.sort()
        # i = j =0
        # result = []
        # while i<len(nums1) and j <len(nums2):
        #     if nums1[i] == nums2[j]:
        #         result.append(nums1[i])
        #         i += 1
        #         j += 1
        #     elif nums1[i] > nums2[j]:
        #         j += 1
        #     else:
        #         i += 1
        # return result

        # 2. 遍历计数
        # if len(nums1) > len(nums2):
        #     return self.intersect(nums2,nums1)
        
        # counter = {}
        # result = []
        # for i in nums1:
        #     counter[i] = counter.get(i,0) + 1
        
        # for j in nums2:
        #     if (exist := counter.get(j,0) > 0):
        #         result.append(j)
        #         counter[j] -= 1

        #         if counter[j] == 0:
        #             counter.pop(j)
        # return result

        # 3 Counter
        from collections import Counter
        c1 = Counter(nums1)
        c2 = Counter(nums2)
        r = c1 & c2
        return list(r.elements())
```

### 加一

给定一个由整数组成的非空数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储单个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

> 示例 1:
>
> 输入: [1,2,3]
> 输出: [1,2,4]
> 解释: 输入数组表示数字 123。
> 示例 2:
>
> 输入: [4,3,2,1]
> 输出: [4,3,2,2]
> 解释: 输入数组表示数字 4321。

```python
class Solution:
    def plusOne(self, digits: List[int]) -> List[int]:

        for i in range(len(digits)-1,-1,-1):
            if digits[i] == 9:
                digits[i] = 0
            else:
                digits[i] += 1
                return digits
        else:
            digits.insert(0,1)
        return digits
```

### 移动零

> 给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。
>
> 示例:
>
> 输入: [0,1,0,3,12]
> 输出: [1,3,12,0,0]
> 说明:
>
> 必须在原数组上操作，不能拷贝额外的数组。
> 尽量减少操作次数。
>

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        zero_i = nums_j = 0
        len_nums = len(nums)
        while zero_i < len_nums and nums_j < len_nums:
            if nums[zero_i] != 0:
                zero_i += 1
                continue

            if nums[nums_j] == 0 or nums_j < zero_i:
                nums_j += 1
                continue
            nums[zero_i], nums[nums_j] = nums[nums_j] ,nums[zero_i]
```

### 两数之和

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

 

> 示例:
>
> 给定 nums = [2, 7, 11, 15], target = 9
>
> 因为 nums[0] + nums[1] = 2 + 7 = 9
> 所以返回 [0, 1]
>

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        # 1. 笨方法
        # for i in range(len(nums)):
        #     if (target - nums[i] ) in nums and nums.index(target-nums[i]) != i:
        #         return [i,nums.index(target-nums[i])]

        # 2. 双循环
        # for i in range(len(nums)):
        #     for j in range(i+1, len(nums)):
        #         if nums[i] + nums[j]  == target:
        #             return [i,j]
        # return []

        # 3. 哈希字典
        hash_dict = dict()
        for index,i in enumerate(nums):
            if (target - i ) in hash_dict.keys():
                return [hash_dict[target - i], index]
            hash_dict[i] = index
        return []

```

### 有效的数独

判断一个 9x9 的数独是否有效。只需要根据以下规则，验证已经填入的数字是否有效即可。

数字 1-9 在每一行只能出现一次。
数字 1-9 在每一列只能出现一次。
数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。


上图是一个部分填充的有效的数独。

数独部分空格内已填入了数字，空白格用 '.' 表示。

> 示例 1:
>
> 输入:
> [
>   ["5","3",".",".","7",".",".",".","."],
>   ["6",".",".","1","9","5",".",".","."],
>   [".","9","8",".",".",".",".","6","."],
>   ["8",".",".",".","6",".",".",".","3"],
>   ["4",".",".","8",".","3",".",".","1"],
>   ["7",".",".",".","2",".",".",".","6"],
>   [".","6",".",".",".",".","2","8","."],
>   [".",".",".","4","1","9",".",".","5"],
>   [".",".",".",".","8",".",".","7","9"]
> ]
> 输出: true
> 示例 2:
>
> 输入:
> [
>   ["8","3",".",".","7",".",".",".","."],
>   ["6",".",".","1","9","5",".",".","."],
>   [".","9","8",".",".",".",".","6","."],
>   ["8",".",".",".","6",".",".",".","3"],
>   ["4",".",".","8",".","3",".",".","1"],
>   ["7",".",".",".","2",".",".",".","6"],
>   [".","6",".",".",".",".","2","8","."],
>   [".",".",".","4","1","9",".",".","5"],
>   [".",".",".",".","8",".",".","7","9"]
> ]
> 输出: false
> 解释: 除了第一行的第一个数字从 5 改为 8 以外，空格内其他数字均与 示例1 相同。
>      但由于位于左上角的 3x3 宫内有两个 8 存在, 因此这个数独是无效的。
> 说明:
>
> 一个有效的数独（部分已被填充）不一定是可解的。
> 只需要根据以上规则，验证已经填入的数字是否有效即可。
> 给定数独序列只包含数字 1-9 和字符 '.' 。
> 给定数独永远是 9x9 形式的。
>

```python
class Solution:
    def isValidSudoku(self, board: List[List[str]]) -> bool:
        row_check_list = [{} for _ in range(9)]
        column_check_list = [{} for _ in range(9)]
        box_check_list = [{} for _ in range(9)]

        for i in range(9):
            for j in range(9):
                if board[i][j] != ".":
                    temp = int(board[i][j])
                    row_check_list[i][temp] = row_check_list[i].get(temp, 0) + 1
                    column_check_list[j][temp] = column_check_list[j].get(temp, 0) + 1
                    box_check_list[3 * (i // 3) + j // 3][temp] = box_check_list[3 * (i // 3) + j // 3].get(temp,0) + 1
                    
                    if any((row_check_list[i].get(temp) > 1 , column_check_list[j].get(temp) > 1 , box_check_list[3 * (i // 3) + j // 3].get(temp) > 1)):
                        return False
        return True


        # row_check_list = [[] for _ in range(9)]
        # column_check_list = [[] for _ in range(9)]
        # square_check_list = [[] for _ in range(9)]

        # for index_line, line in enumerate(board):
        #     for ele_index, ele in enumerate(line):
        #         if ele != ".":
        #             row_check_list[index_line].append(ele)
        #             column_check_list[ele_index].append(ele)
        #             square_check_list[3*(index_line // 3)  + ele_index // 3].append(ele)

        # def check_valid(*lists):
        #     for i in lists:
        #         for j in i:
        #             if len(j) != len(set(j)):
        #                 return False
        #     return True

        return check_valid(row_check_list,column_check_list,square_check_list)
```

### 旋转图像

给定一个 n × n 的二维矩阵表示一个图像。

将图像顺时针旋转 90 度。

说明：

你必须在原地旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要使用另一个矩阵来旋转图像。

> 示例 1:
>
> 给定 matrix = 
> [
>   [1,2,3],
>   [4,5,6],
>   [7,8,9]
> ],
>
> 原地旋转输入矩阵，使其变为:
> [
>   [7,4,1],
>   [8,5,2],
>   [9,6,3]
> ]
> 示例 2:
>
> 给定 matrix =
> [
>   [ 5, 1, 9,11],
>   [ 2, 4, 8,10],
>   [13, 3, 6, 7],
>   [15,14,12,16]
> ], 
>
> 原地旋转输入矩阵，使其变为:
> [
>   [15,13, 2, 5],
>   [14, 3, 4, 1],
>   [12, 6, 8, 9],
>   [16, 7,10,11]
> ]

```python
class Solution:
    def rotate(self, matrix: List[List[int]]) -> None:
        """
        Do not return anything, modify matrix in-place instead.
        """
        # 1.会使用新的空间，但这答案真精髓!!!
        # matrix[:] = map(list,zip(*matrix[::-1]))

        # 2. 先转置矩阵，然后翻转每一行。这个简单的方法已经能达到最优的时间复杂度O(N^2)
        l_matrix = len(matrix)
        for i in range(l_matrix):
            for j in range(i, l_matrix):
                matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
        
        for t in matrix:
            t.reverse()
```

## 字符串

### 反转字符串

编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 char[] 的形式给出。

不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。

你可以假设数组中的所有字符都是 ASCII 码表中的可打印字符。

 

> 示例 1：
>
> 输入：["h","e","l","l","o"]
> 输出：["o","l","l","e","h"]
> 示例 2：
>
> 输入：["H","a","n","n","a","h"]
> 输出：["h","a","n","n","a","H"]
>

```python
class Solution:
    def reverseString(self, s: List[str]) -> None:
        """
        Do not return anything, modify s in-place instead.
        """
        s[:] = s[::-1]
```

### 整数反转

给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

> 示例 1:
>
> 输入: 123
> 输出: 321
>  示例 2:
>
> 输入: -123
> 输出: -321
> 示例 3:
>
> 输入: 120
> 输出: 21
> 注意:
>
> 假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−2^31,  2^31 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。
>

```python
class Solution:
    def reverse(self, x: int) -> int:
        result = int(str(x)[::-1]) if x >=0 else -1 * int(str(x)[1:][::-1])
        if result  < -2 ** 31  or result > 2 ** 31 -1:
            return 0
        else:
            return result
```

### 有效的字母异位词

给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。

> 示例 1:
>
> 输入: s = "anagram", t = "nagaram"
> 输出: true
> 示例 2:
>
> 输入: s = "rat", t = "car"
> 输出: false
> 说明:
> 你可以假设字符串只包含小写字母。
>
> 进阶:
> 如果输入字符串包含 unicode 字符怎么办？你能否调整你的解法来应对这种情况？
>

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        # 1、Counter 大法好
        # from collections import Counter
        # c1 = Counter(s)
        # c2 = Counter(t)
        # return len(c1 - c2) == 0 and len(c2 - c1) == 0
        
        # 2、 sort 大法好
        #return sorted(s) == sorted(t)

				# 3、 哈希计数法
        if len(s) != len(t):
            return False
        result_dict = dict()
        for i in range(len(s)):
            result_dict[s[i]] = result_dict.get(s[i],0) + 1
            result_dict[t[i]] = result_dict.get(t[i],0) - 1
        for k,v in result_dict.items():
            if v != 0:
                return False
        return True
```

### 验证回文串

给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

说明：本题中，我们将空字符串定义为有效的回文串。

> 示例 1:
>
> 输入: "A man, a plan, a canal: Panama"
> 输出: true
> 示例 2:
>
> 输入: "race a car"
> 输出: false

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        if len(s) <= 1:
            return True
        i = 0
        j = len(s)-1
        while i < j:
            while not s[i].isalnum():
                i += 1
                if i == j:
                    return True
            while not s[j].isalnum():
                j -= 1
                if i == j:
                    return True
            if s[i].lower() == s[j].lower():
                i += 1
                j -= 1
            else:
                return False
        return True
```

### 字符串转换整数 (atoi)

请你来实现一个 atoi 函数，使其能将字符串转换成整数。

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。接下来的转化规则如下：

* 如果第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字字符组合起来，形成一个有符号整数。

* 假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成一个整数。
* 该字符串在有效的整数部分之后也可能会存在多余的字符，那么这些字符可以被忽略，它们对函数不应该造成影响。
* 注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换，即无法进行有效转换。

* 在任何情况下，若函数不能进行有效的转换时，请返回 0 。

提示：

本题中的空白字符只包括空格字符 ' ' 。
假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [−2^31,  2^31 − 1]。如果数值超过这个范围，请返回  INT_MAX (231 − 1) 或 INT_MIN (−231) 。

>
> 示例 1:
>
> 输入: "42"
> 输出: 42
> 示例 2:
>
> 输入: "   -42"
> 输出: -42
> 解释: 第一个非空白字符为 '-', 它是一个负号。
>      我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。
> 示例 3:
>
> 输入: "4193 with words"
> 输出: 4193
> 解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。
> 示例 4:
>
> 输入: "words and 987"
> 输出: 0
> 解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
>      因此无法执行有效的转换。
> 示例 5:
>
> 输入: "-91283472332"
> 输出: -2147483648
> 解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
>      因此返回 INT_MIN (−231) 。
>

```python
class Solution:
    def myAtoi(self, s: str) -> int:
        new_s = s.lstrip()
        if not new_s:
            return 0

        flag = 1
        result = 0

        if new_s[0] in ["+","-"]:
            flag = -1 if new_s[0] == "-" else 1
            new_s = list(new_s)[1:]
        elif new_s[0].isdigit:
            pass
        else:
            return 0

        for i in new_s:
            if i.isdigit():
                result = result * 10 + int(i)
            else:
                break
        result =  flag  * result
        if result >= 2**31 -1:
            return 2**31 -1 
        elif result <= -1 * (2 ** 31):
            return -1 * (2 ** 31)
        else:
            return result
```

### 实现 strStr()

实现 strStr() 函数。

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

> 示例 1:
>
> 输入: haystack = "hello", needle = "ll"
> 输出: 2
> 示例 2:
>
> 输入: haystack = "aaaaa", needle = "bba"
> 输出: -1
> 说明:
>
> 当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。
>
> 对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与C语言的 strstr() 以及 Java的 indexOf() 定义相符。
>

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        if not needle:
            return 0
        # 1、暴力法
        # find_flag = False
        # for i in range(len(haystack)-len(needle)+1):
        #     t = i
        #     for j in needle:
        #         if t < len(haystack)  and haystack[t] == j:
        #             t += 1
        #             continue
        #         else:
        #             break
        #     else:
        #         find_flag = True
            
        #     if find_flag:
        #         return i
        # return -1

				# 2、切片暴力法
        l_h = len(haystack)
        l_n = len(needle)
        for i in range(l_h - l_n + 1):
            if haystack[i:i+l_n] == needle:
                return i
        return -1
```

### 外观数列

给定一个正整数 n ，输出外观数列的第 n 项。

「外观数列」是一个整数序列，从数字 1 开始，序列中的每一项都是对前一项的描述。

你可以将其视作是由递归公式定义的数字字符串序列：

countAndSay(1) = "1"
countAndSay(n) 是对 countAndSay(n-1) 的描述，然后转换成另一个数字字符串。
前五项如下：

1.     1
2.     11
3.     21
4.     1211
5.     111221
第一项是数字 1 
描述前一项，这个数是 1 即 “ 一 个 1 ”，记作 "11"
描述前一项，这个数是 11 即 “ 二 个 1 ” ，记作 "21"
描述前一项，这个数是 21 即 “ 一 个 2 + 一 个 1 ” ，记作 "1211"
描述前一项，这个数是 1211 即 “ 一 个 1 + 一 个 2 + 二 个 1 ” ，记作 "111221"
要 描述 一个数字字符串，首先要将字符串分割为 最小 数量的组，每个组都由连续的最多 相同字符 组成。然后对于每个组，先描述字符的数量，然后描述字符，形成一个描述组。要将描述转换为数字字符串，先将每组中的字符数量用数字替换，再将所有描述组连接起来。

例如，数字字符串 "3322251" 的描述如下图：

![图](../images/all/countandsay.png)

>
> 示例 1：
>
> 输入：n = 1
> 输出："1"
> 解释：这是一个基本样例。
> 示例 2：
>
> 输入：n = 4
> 输出："1211"
> 解释：
> countAndSay(1) = "1"
> countAndSay(2) = 读 "1" = 一 个 1 = "11"
> countAndSay(3) = 读 "11" = 二 个 1 = "21"
> countAndSay(4) = 读 "21" = 一 个 2 + 一 个 1 = "12" + "11" = "1211"
>
>
> 提示：
>
> 1 <= n <= 30

```python
class Solution:
    def countAndSay(self, n: int) -> str:
        def count_say(strs):
            result = []
            pre = None
            for i in strs:
                if i == pre:
                    result[-2] = str(int(result[-2]) + 1)
                else:
                    result.append("1")
                    result.append(i)
                    pre = i
            return "".join(result)  

        if n == 1:
            return "1"
        else:
            return count_say(self.countAndSay(n-1))

```



### 最长公共前缀

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。

> 示例 1:
>
> 输入: ["flower","flow","flight"]
> 输出: "fl"
> 示例 2:
>
> 输入: ["dog","racecar","car"]
> 输出: ""
> 解释: 输入不存在公共前缀。
> 说明:
>
> 所有输入只包含小写字母 a-z 。
>

```python
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
      	# 1、暴力法
        # if len(strs) == 0:
        #     return ""
        # min_s = min(strs, key=lambda x: len(x))
        # result = ""
        # for i in min_s:
        #     result += i
        #     for item in strs:
        #         if not item.startswith(result):
        #             return result[:-1]
        # return result
				
        # 2.纵向比对
        if len(strs) == 0:
            return ""
        lenth, count = len(strs[0]), 0
        for i in range(lenth):
            c = strs[0][i]
            if any(i == len(j) or j[i] != c for j in strs[1:]):
                return strs[0][:i]
        return strs[0]
```

# 链表

## 反转链表

反转一个单链表。

> 示例:
>
> 输入: 1->2->3->4->5->NULL
> 输出: 5->4->3->2->1->NULL
> 进阶:
> 你可以迭代或递归地反转链表。你能否用两种方法解决这道题？

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        # 1.迭代
        # pre_node = None
        # while head:
        #     next_node = head.next
        #     head.next = pre_node
        #     pre_node = head
        #     head = next_node
        # return pre_node
				
        # 2.递归
        if head is None or head.next is None:
            return head
        p = self.reverseList(head.next)
        head.next.next = head
        head.next = None
        return p

```

# 其他

## 判断IP

给定一组字符串输入一个ip地址判断是否是正确的ip，输出正确的ip

```python
def isIpv4(self, s):
  import re
  pattern = r"(\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])(\.(\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])){3}"
  if re.match(pattern, s):
    return True
  else:
    return False
```

# 牛客

# 最长公共子序列

# 最长公共子串

题目描述

给定两个字符串str1和str2,输出两个字符串的最长公共子串，如果最长公共子串为空，输出-1。



示例1

输入

```
"1AB2345CD","12345EF"
```

返回值

```
"2345"
```

备注:

```
1≤str1,str2≤5000
```

