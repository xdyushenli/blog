---
title: 纪念一下
date: 2019-09-12 19:28:17
tags: [LeetCode]
---
纪念一下第一个超过了100%用户的LeetCode题。
![](/images/first-100/first-10001.png)

```js
/**
* @param {number} n
* @param {number} k
* @return {string}
*/
var getPermutation = function (n, k) {
    let ans = '';

    let factorial = function (n) {
        if (n == 1) return 1;
        else return n * factorial(n - 1)
    }

    let getPermutationRecursive = function (n, k) {
        if (n.length === 1) {
            ans += n[0];
            return ans;
        }

        let lastRound = factorial(n.length - 1);
        let currentIndex = ~~(k / lastRound);
        let currentNumber = n[currentIndex];
        let nextK = k % lastRound;

        n.splice(currentIndex, 1);

        ans = ans + currentNumber + getPermutationRecursive(n, nextK);
        return ans;
    }

    let arr = [];

    for (let i = 1; i <= n; i++) {
        arr.push(i);
    }

    getPermutationRecursive(arr, k - 1)

    return ans;
};
```
