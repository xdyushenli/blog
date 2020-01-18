---
title: 十大经典的排序算法
date: 2020-01-16 20:02:50
tags: [ 算法 ]
---
# 简介
十大排序算法应该是每个程序员必会的知识, 之前虽然会用到, 但是并没有系统地总结过, 因此这里做一次总结。
十大排序算法分别是: 
* [冒泡排序](#冒泡排序)
* [选择排序](#选择排序)
* [插入排序](#插入排序)
* [归并排序](#归并排序)
* [快速排序](#快速排序)
* [堆排序](#堆排序)
* [桶排序](#桶排序)
* [希尔排序](#希尔排序)
* [基数排序](#基数排序)
* [计数排序](#计数排序)

## 排序算法的稳定性
排序算法的稳定性是指, 能保证两个相等的元素在排序前和排序后的相对顺序是相同的。

# 冒泡排序
## 思路
先比较第一个和第二个元素, 若第一个元素大于第二个元素, 则交换位置。之后以相同的规则比较第二个和第三个元素、第三个和第四个元素, 直到最后一对相邻的元素为止。这样一趟比较下来, 最大的元素便会跑到数组的最后。
之后除去最后一个元素, 对剩余的元素重复上述过程, 直到排序完成。
有一个值得优化的地方, 如果数组本身就是有序的, 则第一趟比较时不会发生交换, 那么我们便可以直接返回。

## 实现
```js
function bubbleSorting(arr) {
    if(arr.length <= 1) {
        return arr;
    }

    let ArrayIsSorted = true;
    
    for(let i = arr.length - 1; i > 0; i--) {
        for(let j = 1; j <= i; j++) {
            if(arr[j] < arr[j - 1]) {
                ArrayIsSorted = false;
                // 交换两个元素的位置
                [arr[j], arr[j - 1]] = [arr[j - 1], arr[j]]
            }
        }

        // 数组是排序好的数组
        if(ArrayIsSorted) {
            break;
        }
    }

    return arr;
}
```

## 总结
* **时间复杂度: O(n^2)**
* **空间复杂度: O(1)**
* 冒泡排序属于**稳定**的排序算法, 是原地排序。

# 选择排序
## 思路
首先, 从数组中找到最小的元素, 和第一个元素交换位置。之后排除第一个元素, 在剩余元素中找到最小的元素, 与第二个元素交换位置。直到所有元素都完成排序为止。

## 实现
```js
function selectSort(arr) {
    if(arr.length <= 1) {
        return arr;
    }

    for(let i = 0; i < arr.length; i++) {
        let min = arr[i];
        let minIndex = i;

        for(let j = i; j < arr.length; j++) {
            if(arr[j] < min) {
                min = arr[j];
                minIndex = j;
            }
        }

        [arr[i], arr[minIndex]] = [arr[minIndex], arr[i]];
    }

    return arr;
}
```

## 总结
* **时间复杂度: O(n^2)**
* **空间复杂度: O(1)**
* 选择排序属于**不稳定**的排序算法, 是原地排序。

# 插入排序
## 思路
插入排序, 顾名思义, 就是把元素 "插入" 到它应该在的位置。
从第二个元素开始, 与左边的元素进行比较。若当前元素比与其左侧相邻的元素小, 则交换二者位置。之后继续将当前元素与左边第二个元素做比较, 直到左侧没有元素或遇到小于等于当前元素的元素为止。
为了方便理解, 可以看下面的动图。
![插入排序](/images/sorting-algorithm/01.webp)

## 实现
```js
function insertSort(arr) {
    if(arr.length <= 1) {
        return arr;
    }

    for(let i = 1; i < arr.length; i++) {
        for(let j = i; j > 0; j--) {
            // 若左侧元素比当前元素小, 则跳出循环
            if(arr[j - 1] <= arr[j]) {
                break;
            } else {
                // 交换相邻元素的位置
                [arr[j], arr[j - 1]] = [arr[j - 1], arr[j]];
            }
        }
    }

    return arr;
}
```

## 总结
* **时间复杂度: O(n^2)**
* **空间复杂度: O(1)**
* 插入排序属于**稳定**的排序算法, 是原地排序。

# 归并排序
## 思路
简单来说, 归并排序就是指将一个大的无序数组分成两个, 然后分别对两个数组进行排序, 最后再将两个排序后的小数组合并为一个有序的大数组。
为了方便理解, 可以看下面的动图:
![归并排序](/images/sorting-algorithm/02.webp)

## 实现
```js
function mergeSort(arr) {
    if(arr.length <= 1) {
        return arr;
    }

    let left = mergeSort(arr.slice(0, arr.length >> 1));
    let right = mergeSort(arr.slice(arr.length >> 1));
    let ans = [];

    // 合并两个有序数组
    for(let i = 0, j = 0; i < left.length || j < right.length; ) {
        // 其中一个数组所有元素都已经放入 ans 中
        if(!left[i]) {
            ans = ans.concat(right.slice(j))
            break;
        }

        if(!right[j]) {
            ans = ans.concat(left.slice(i))
            break;
        }

        // 比较左右两个数组元素的大小
        if(left[i] <= right[j]) {
            ans.push(left[i]);
            i++;
        } else {
            ans.push(right[j]);
            j++;
        }
    }

    return ans;
}
```

## 总结
* **时间复杂度: O(nlgn)**
* **空间复杂度: O(n)**
* 归并排序属于**稳定**的排序算法, 不是原地排序。

# 快速排序
## 思路
从数组中选择一个元素, 作为中轴元素。所有比此元素大的都放在该元素右边, 所有比此元素小的都放在该元素左边。
之后采用递归的方式, 对中轴元素左右两边的数组 (不包含中轴元素) 重复这一过程即可。

## 实现
首先我们来看一下阮一峰阮大大的解法:
```js
function quickSort(arr) {
    if(arr.length <= 1) {
        return arr;
    }

    let midIndex = arr.length >> 1;
    let midNumber = arr.splice(midIndex, 1)[0];
    let left = [];
    let right = [];

    for(let ele of arr) {
        if(ele < midNumber) {
            left.push(ele);
        } else {
            right.push(ele);
        }
    }

    return quickSort(left).concat([midNumber], quickSort(right));
}

```
这种写法通俗易懂, 但是使用了额外的空间, 并且是一种不稳定的排序。因此让我们来改进下。
```js
function quickSort(arr, left = 0, right = arr.length - 1) {
    if (right - left <= 0) {
        return arr;
    }

    let midIndex = (left + right + 1) >> 1;
    let midNumber = arr[midIndex];

    for (let leftIndex = left, rightIndex = right; leftIndex <= midIndex && rightIndex >= midIndex;) {
        // 找到左侧第一个大于中轴元素的元素
        while (arr[leftIndex] <= midNumber && leftIndex < midIndex) {
            leftIndex++;
        }

        // 找到右侧第一个小于中轴元素的元素
        while (arr[rightIndex] >= midNumber && rightIndex > midIndex) {
            rightIndex--;
        }

        // 如果在左侧没找到大于中轴的元素
        if (leftIndex == midIndex) {
            for (let i = rightIndex; i > midIndex;) {
                // 若右侧有小于中轴的元素
                if (arr[i] < midNumber) {
                    let element = arr[i];
                    // 删除右侧的元素
                    arr.splice(i, 1);
                    // 将删除的元素插入到 midIndex 之前
                    arr.splice(midIndex, 0, element);
                    // 由于前插了元素, 因此需要增加 midIndex
                    midIndex++;
                } else {
                    i--;
                }
            }

            break;
        } else if (rightIndex == midIndex) {
            // 如果在右侧没有找到大于中轴的元素
            for (let i = leftIndex; i < midIndex;) {
                // 若左侧有大于中轴的元素
                if (arr[i] > midNumber) {
                    let element = arr[i];
                    // 删除右侧的元素
                    arr.splice(i, 1);
                    // 将删除的元素插入到 midIndex 之后
                    arr.splice(midIndex, 0, element);
                    // 由于前面删除了元素, 因此需要减小 midIndex
                    midIndex--;
                } else {
                    i++;
                }
            }

            break;
        } else {
            // 交换左右两侧元素的位置
            [arr[leftIndex], arr[rightIndex]] = [arr[rightIndex], arr[leftIndex]];
            leftIndex++;
            rightIndex--;
        }
    }

    quickSort(arr, left, midIndex - 1);
    quickSort(arr, midIndex + 1, right);

    return arr;
}
```

## 总结
* **时间复杂度: O(nlgn)**
* **空间复杂度: O(1)**
* 快速排序属于**稳定**的排序算法, 是原地排序。

# 堆排序
## 思路
## 实现
## 总结
* **时间复杂度: O(n^2)**
* **空间复杂度: O(1)**
* 选择排序属于**不稳定**的排序算法, 是原地排序。

# 桶排序
## 思路
## 实现
## 总结
* **时间复杂度: O(n^2)**
* **空间复杂度: O(1)**
* 选择排序属于**不稳定**的排序算法, 是原地排序。

# 希尔排序
## 思路
## 实现
## 总结
* **时间复杂度: O(n^2)**
* **空间复杂度: O(1)**
* 选择排序属于**不稳定**的排序算法, 是原地排序。

# 基数排序
## 思路
## 实现
## 总结
* **时间复杂度: O(n^2)**
* **空间复杂度: O(1)**
* 选择排序属于**不稳定**的排序算法, 是原地排序。

# 计数排序
## 思路
## 实现
## 总结
* **时间复杂度: O(n^2)**
* **空间复杂度: O(1)**
* 选择排序属于**不稳定**的排序算法, 是原地排序。
