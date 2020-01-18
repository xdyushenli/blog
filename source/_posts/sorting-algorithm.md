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
* [希尔排序](#希尔排序)
* [归并排序](#归并排序)
* [快速排序](#快速排序)
* [堆排序](#堆排序)
* [计数排序](#计数排序)
* [桶排序](#桶排序)
* [基数排序](#基数排序)

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

# 希尔排序
## 思路
希尔排序可以看做是插入排序的一种变种。
不管是冒泡排序还是插入排序, 比较的都是相邻元素之间的大小。如果一个很大的元素处于很靠前的位置, 那么需要移动很多次才能到达它应该在的位置。使用希尔排序, 通过让大元素与不相邻的元素进行比较, 减少其的移动次数, 从而提高时间效率。
希尔排序的思想是, 先让相隔为 gap 的元素彼此之间有序 (此排序过程使用插入排序), 之后逐渐缩小间隔 gap 值, 当 gap = 1 时, 整个数组便是有序的了。
![希尔排序](/images/sorting-algorithm/03.jpg)
需要注意的是, **对各个分组进行插入的时候并不是先对一个组排序完了再来对另一个组排序, 而是轮流对每个组进行排序。**
## 实现
```js
function shellSort(arr) {
    if(arr.length <= 1) {
        return arr;
    }

    // 不断减小 gap 直至为 1
    for (let gap = arr.length >> 1; gap > 0; gap = gap >> 1) {
        // 对局部进行排序
        for (let i = gap; i < arr.length; i++) {
            // 遍历 arr[i] 所属的组中在 arr[i] 之前的元素, 将 arr[i] 放在合适的位置
            for (let j = i - gap; j >= 0; j -= gap) {
                if (arr[i] < arr[j]) {
                    [arr[i], arr[j]] = [arr[j], arr[i]];
                }
            }
        }
    }

    return arr;
}
```

## 总结
* **时间复杂度: O(nlgn)**
* **空间复杂度: O(1)**
* 希尔排序属于**不稳定**的排序算法, 是原地排序。

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
堆有两种, 堆顶是最大值的堆被称为`大顶堆`, 堆顶是最小值的堆被称为`小顶堆`。
堆排序就是把大顶堆的堆顶元素和最后一个元素交换位置, 之后再把剩余元素继续组成大顶堆, 把堆顶与倒数第二个元素交换位置, 然后重复这一过程。

## 实现
```js
function heapSort(arr) {
    if(arr.length <= 1) {
        return arr;
    }
    
    // 构建大顶堆
    for (let i = arr.length >> 1; i >= 0; i--) {
        downAdjust(arr, i, arr.length - 1);
    }

    // 进行堆排序
    for (let i = arr.length - 1; i >= 1; i--) {
        // 把堆顶元素与最后一个元素交换
        [arr[0], arr[i]] = [arr[i], arr[0]];
        // 继续用剩余元素组成大顶堆
        downAdjust(arr, 0, i - 1);
    }

    return arr;
}

function downAdjust(arr, parent, len) {
    //临时保存要下沉的元素
    let temp = arr[parent];
    //定位左孩子节点的位置
    let child = 2 * parent + 1;
    //开始下沉
    while (child <= len) {
        // 如果右孩子节点比左孩子大，则定位到右孩子
        if (child + 1 <= len && arr[child] < arr[child + 1])
            child++;
        // 如果孩子节点小于或等于父节点，则下沉结束
        if (arr[child] <= temp) break;
        // 父节点进行下沉
        arr[parent] = arr[child];
        parent = child;
        child = 2 * parent + 1;
    }
    arr[parent] = temp;
}
```
## 总结
* **时间复杂度: O(nlgn)**
* **空间复杂度: O(1)**
* 堆排序属于**不稳定**的排序算法, 是原地排序。
* 堆这种数据结构存在的意义?
如果需要的仅仅是一个序列, 使用排序很快就可以完成。但是, 如果我们面对的是一个随时会更新的序列, 那么我们既需要把更新的元素正确的插入序列中, 也需要在序列被划分为任意子集时可以得出子集的最大值和最小值。这个时候, 堆便能派上用场啦。

# 计数排序
## 思路
计数排序的基本思想是: 把数组元素作为数组的下标, 原数组中每有一个对应的数组元素, 就在新数组中以此元素下标的、对应的元素上加一。最后统计新数组各个元素的值, 然后创建新数组。

## 实现
```js
function countSort(arr) {
    if(arr.length <= 1) {
        return arr;
    }

    let count = [];
    let ans = [];

    // 统计原数组中各元素出现的次数
    for(let i = 0; i < arr.length; i++) {
        let index = arr[i];
        if(count[index] !== undefined) {
            count[index]++;
        } else {
            count[index] = 1;
        }
    }

    // 创建新数组
    for(let i = 0; i < count.length; i++) {
        if(arr[i] !== undefined) {
            while(arr[i] > 0) {
                ans.push(i);
                arr[i]--;
            }
        }
    }

    return ans;
}
```

## 总结
* **时间复杂度: O(n)**
* **空间复杂度: O(n)**
* 计数排序属于**不稳定**的排序算法, 不是原地排序。
* 计数排序有**两个缺陷**: 一个是如果原数组最大值和最小值相差很大, 那么需要的额外空间也很大, 并且中间会有许多空白, 造成不必要的浪费。二是如果原数组中的元素不是正整数, 那么就无法使用该方法。

# 桶排序
## 思路
桶排序的基本思路是, 将最大值和最小值中间数等分为几个区间, 每个区间称为桶。将数组元素放入桶中, 每个桶中都是有序的。最后把各个桶之间汇总起来, 形成有序的数组。
![桶排序](/images/sorting-algorithm/04.jpg)

## 实现
```js
function bucketSort(arr) {
    if(arr.length <= 1) {
        return arr;
    }

    // 查找最大值和最小值
    let max = arr[0];
    let min = arr[0];

    for(let ele of arr) {
        if(ele < min) {
            min = ele;
        } else if(ele > max) {
            max = ele;
        }
    }

    // 创建容量为 5 的桶
    let buckets = [];

    // 将原数组元素放入桶中
    for(let ele of arr) {
        // 注意: 若桶的容量为 n, 那么除的量就应该为 n - 2
        let bucketIndex = Math.floor((ele - min) / 3);

        if(buckets[bucketIndex] === undefined) {
            buckets[bucketIndex] = [ele];
        } else {
            let bucket = buckets[bucketIndex];
            let index = 0;

            while(bucket[index] < ele && index < bucket.length) {
                index++;
            }

            bucket.splice(index, 0, ele);
        }
    }

    // 创建新数组
    let ans = [];

    for(bucket of buckets) {
        ans = ans.concat(bucket);
    }

    return ans;
}
```

## 总结
* **时间复杂度: O(n)**
* **空间复杂度: O(n)**
* 桶排序属于**稳定**的排序算法, 不是原地排序。

# 基数排序
## 思路
基数排序的基本思路是: 先按照个位数来排序, 再按照十位数来排序, 然后是百位数、千位数等, 排到最高位的时候, 就是一组有序的元素了。在各个位进行排序时, 是使用桶来进行排序的。

![基数排序](/images/sorting-algorithm/05.webp)

## 实现
```js
function radixSort(arr) {
    if (arr.length <= 1) {
        return arr;
    }

    // 找出最高位
    let maxRadix = 1;
    for (let ele of arr) {
        let currentEle = ele;
        let currentRadix = 1;

        while (currentEle >= 10) {
            currentEle /= 10;
            currentRadix++;
        }

        if (currentRadix > maxRadix) {
            maxRadix = currentRadix;
        }
    }

    // 创建容量为 10 的桶
    let buckets = [];

    // 按照不同位进行排序
    let radix = 0;
    while (radix < maxRadix) {
        // 将对应数字放入桶中
        for (let ele of arr) {
            let currentEle = ele + '';
            let bucketIndex = radix == 0 ? currentEle.slice(-1) : currentEle.slice(-radix - 1, -radix) == '' ? 0 : currentEle.slice(-radix - 1, -radix);

            if (buckets[bucketIndex] === undefined) {
                buckets[bucketIndex] = [ele];
            } else {
                let bucket = buckets[bucketIndex];
                let index = 0;

                while (bucket[index] < ele && index < bucket.length) {
                    index++;
                }

                bucket.splice(index, 0, ele);
            }
        }

        // 汇总合并后重新放入原数组中
        arr = [];
        for (let i = 0; i < 10; i++) {
            let bucket = buckets[i] === undefined ? [] : buckets[i];
            arr = arr.concat(bucket);
            // 清空桶中数据
            buckets[i] = [];
        }

        radix++;
    }

    return arr;
}
```

## 总结
* **时间复杂度: O(n)**
* **空间复杂度: O(n)**
* 基数排序属于**不稳定**的排序算法, 不是是原地排序。
* 基数排序**只对全是正整数的数组有效,** 对于有小数或负数存在的数组无能为力。

# 总结
![总结](/images/sorting-algorithm/06.png)
