---
title: js中的数字
date: 2019-09-11 16:10:31
tags: [js, LeetCode]
---
这篇文章是在刷LeetCode中第29题: [两数相除](https://leetcode-cn.com/problems/divide-two-integers/submissions/)时想到的。
## js中的数字
js中所有的数字都是以32位有符号数进行存储的。因此在进行移位运算时应该格外小心，一不小心就会溢出造成奇奇怪怪的结果。
## 题目思路
由于题干中不允许使用乘除法和取余运算，因此就只能用加减法来进行除法运算。但是商一次加一太耗费时间，因此对除数进行移位运算，呈指数级增长，可以大大缩短运算所需的时间。
## 代码编写
```js
/**
 * @param {number} dividend
 * @param {number} divisor
 * @return {number}
 */
var divide = function(dividend, divisor) {
    if(dividend === 0 || divisor === 0) return 0;
    
    let isPositive = !(dividend < 0 ^ divisor < 0);
    let ans = 0
    
    dividend = Math.abs(dividend);
    divisor = Math.abs(divisor);
    
    while(dividend >= divisor) {
        // 重置除数
        let tempDivisor = divisor;
        let count = 1;
        
        // js存放的都是32位有符号整数，因此最大二进制数只能是2147483648
        // 移位不仅可能导致上溢，还有可能导致下溢，所以要对tempDivisor进行界定
        while(dividend >= tempDivisor && tempDivisor <= 2147483648 && tempDivisor >= divisor) {
            dividend -= tempDivisor;
            ans += count;
            tempDivisor = tempDivisor << 1;
            count = count << 1;
        }
    }
    
    ans = isPositive ? ans : -ans;
    
    if(ans > 2147483647 || ans < -2147483648) {
        return 2147483647;
    } else {
        return ans;   
    }
};
```
