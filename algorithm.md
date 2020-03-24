#### 排序

[阮一峰排序](https://javascript.ruanyifeng.com/library/sorting.html#toc12)

- 五大算法
  - 排序算法：快速排序、归并排序、计数排序
  - 搜索算法：回溯、递归、剪枝技巧 [切割回文数-LeetCode]
  - 图论：最短路、最小生成树
  - 动态规划：背包问题、最长子序列、计数问题 https://labuladong.gitbook.io/algo/dong-tai-gui-hua-xi-lie/bei-bao-wen-ti
  - 基础技巧：分治、倍增、二分、贪心

数据结构

- 数组与链表：单 / 双向链表、跳舞链
- 栈与队列
- 树与图：最近公共祖先、并查集
- 哈希表
- 堆：大 / 小根堆、可并堆
- 字符串：字典树、后缀树

* 冒泡排序  
  冒泡排序的原理：从第一个元素开始，把当前元素和下一个索引元素进行比较，相邻两个数两两对比，如果当前元素大则交换位置，重复操作直到最后一个元素，则此时最后一个元素就是该数组中最大的数。下一轮重复以上操作
  ```javascript
  function bubbleSort(arr) {
    for (let i = arr.length; i >= 2; i--) {
      for (let j = 0; j <= i - 1; j++) {
        if (arr[j] > arr[j + 1]) {
          // 解构
          ;[arr[j], arr[j + 1]] = [arr[j + 1], arrp[j]]
        }
      }
    }
  }
  ```
  冒泡排序是最容易实现的排序, 最坏的情况是每次都需要交换, 共需遍历并交换将近 n²/2 次, 时间复杂度为 O(n²). 最佳的情况是内循环遍历一次后发现排序是对的, 因此退出循环, 时间复杂度为 O(n). 平均来讲, 时间复杂度为 O(n²). 由于冒泡排序中只有缓存的 temp 变量需要内存空间, 因此空间复杂度为常量 O(1).
  ![bubble](./image/bubble.gif?raw=true '冒泡排序')

- 插入排序  
  原理：第一个元素默认是已排序元素，取出下一个元素和当前元素比较，如果当前元素大就交换位置，那么此时的第一个元素就是当前的最小数，所以下次取出操作从第三个元素开始，向前对比，重复操作。简单来说就是将元素插入到已排序好的数组中

  ```javascript
  function insertion(arr) {
    for (let i = 1; i < arr.length; i++) {
      // 外循环从1开始，默认arr[0]是有序段
      for (let j = i; j > 0; j--) {
        if (arr[j] < arr[j - 1]) {
          ;[arr[j], arr[j - 1]] = [arr[j - 1], arr[j]]
        } else {
          break
        }
      }
    }
  }
  ```

  ![insertion](./image/insertion.gif?raw=true '插入排序')

- 选择排序  
  原理：默认第一个元素是已经排好序的元素，遍历其他元素和它对比，一轮比较完毕后，找到最大（或最小值）之后，将其与第一个值交换，其他数的位置不变。

  ```javascript
  function selection(arr) {
    var len = arr.length,
      min
    for (i = 0; i < len; i++) {
      // 默认第一个元素是已经排好序的元素，记录最小值的下标
      min = i

      for (j = i + 1; j < len; j++) {
        if (arr[j] < arr[min]) {
          min = j
        }
      }

      if (i != min) {
        ;[arr[min], arr[i]] = [arr[i], arr[min]]
      }
    }
    return array
  }
  ```

- 快速排序

  - 选择基准值(base), 原数组长度减一(基准值), 使用 splice
  - 循环原数组, 小的放左边(left 数组), 大的放右边(right 数组)
  - concat(left, right)
  - 递归继续排序 left 与 right

  ```javascript
  function quickSort(arr) {
    if (arr.length <= 1) {
      return arr // 递归出口
    }

    var left = [],
      right = [],
      current = arr.splice(0, 1) // 取第一个为基准
    for (let i = 0; i < arr.length; i++) {
      if (arr[i] < current) {
        left.push(arr[i]) // 放在左边
      } else {
        right.push(arr[i]) // 放在右边
      }
    }
    return quickSort(left).concat(current, quickSort(right))
  }
  ```

  - 优化：如果排列的数组存在大量重复元素，可以使用三向切分的快速排序提高效率
  - 即，加多一个数组存储等于基准的元素

  ```javascript
  function quick3Sort(arr) {
    ...
    for (let i = 0; i < arr.length; i++) {
      ...
      else {
        mid.push(arr[i])
      }
    }
    return quickSort(left).concat(mid, quickSort(right))
  }
  ```

  - 一行代码实现

  ```javascript
  function quickSort(arr) {
    return arr.length <= 1
      ? arr
      : quickSort(arr.slice(1).filter(item => item <= arr[0])).concat(
          arr[0],
          quickSort(arr.slice(1).filter(item => item > arr[0]))
        )
  }
  ```

- 归并排序
  该算法的核心是：如何合并两个已经排好序的数组？去两者其轻

  ```js
  function merge(left, right) {
    let result = []
    let i = 0,
      j = 0
    while (i < left.length && j < right.length) {
      if (left[i] <= right[j]) {
        result.push(left[i++])
      } else {
        result.push(right[j++])
      }
    }
    if (i < left.length) {
      result.push(...left.slice(i))
    } else {
      result.push(...right.slice(j))
    }
    return result
  }

  function mergeSort(array) {
    if (array.length < 2) {
      return array
    }

    let m = Math.floor(array.length / 2)
    let left = mergeSort(array.slice(0, m))
    let right = mergeSort(array.slice(m))
    return merge(left, right)
  }
  ```

- 计数排序
  统计每个元素重复出现的次数，从小到大排序
  ```js
  function countingSort(array) {
    let min = Infinity
    for (let v of array) {
      if (v < min) {
        min = v
      }
    }
    let counts = []
    for (let v of array) {
      counts[v - min] = (counts[v - min] || 0) + 1
    }
    let index = 0
    for (let i = 0; i < counts.length; i++) {
      let count = counts[i]
      while (count > 0) {
        array[index] = i + min
        count--
        index++
      }
    }
    return array
  }
  ```

* 遍历节点

- 前序遍历
- 根节点
- 访问左子节点，回到 1
- 访问右子节点，回到 1
- 中序遍历
- 先访问到最左的子节点
- 访问该节点的父节点
- 访问该父节点的右子节点， 回到 1
- 后序遍历
- 先访问到最左的子节点
- 访问相邻的右节点
- 访问父节点， 回到 1

````

- 实现一个算法，来完成字符串相加，比如 "111" + ”2222“ = ”2333“
  思路：模拟实现两个整数相加
```js
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
var addStrings = function(num1, num2) {
    let i = num1.length - 1
    let j = num2.length - 1
    let carry = 0
    let temp = 0
    let res = ''

    while(i >= 0 || j >= 0) {
        let numi = i >= 0 ? Number(num1[i]) : 0
        let numj = j >= 0 ? Number(num2[j]) : 0
        temp = numi + numj + carry
        carry = Math.floor(temp / 10)
        res = String(temp % 10) + res
        i--
        j--
    }

    return carry ? '1' + res : res
};
````

````
有 8 个图片资源的 url，已经存储在数组 urls 中（即urls = [‘http://example.com/1.jpg’, …., ‘http://example.com/8.jpg’]），而且已经有一个函数 function loadImg，输入一个 url 链接，返回一个 Promise，该 Promise 在图片下载完成的时候 resolve，下载失败则 reject。但是我们要求，任意时刻，同时下载的链接数量不可以超过 3 个。请写一段代码实现这个需求，要求尽可能快速地将所有图片下载完成。

现有代码如下：
  var urls = [
  'https://www.kkkk1000.com/images/getImgData/getImgDatadata.jpg',
  'https://www.kkkk1000.com/images/getImgData/gray.gif',
  'https://www.kkkk1000.com/images/getImgData/Particle.gif',
  'https://www.kkkk1000.com/images/getImgData/arithmetic.png',
  'https://www.kkkk1000.com/images/getImgData/arithmetic2.gif',
  'https://www.kkkk1000.com/images/getImgData/getImgDataError.jpg',
  'https://www.kkkk1000.com/images/getImgData/arithmetic.gif',
  'https://www.kkkk1000.com/images/wxQrCode2.png'
  ];
  function loadImg(url) {
      return new Promise((resolve, reject) => {
          const img = new Image()
          img.onload = function () {
              console.log('一张图片加载完成');
              resolve();
          }
          img.onerror = reject
          img.src = url
      })
  };

````

解法：
```js
function limitLoad(urls, handler, limit) {
    // 对数组做一个拷贝
    const sequence = [].concat(urls)
    let promises = [];

    //并发请求到最大数
    promises = sequence.splice(0, limit).map((url, index) => {
        // 这里返回的 index 是任务在 promises 的脚标，
        //用于在 Promise.race 之后找到完成的任务脚标
        return handler(url).then(() => {
            return index
        });
    });

    (async function loop() {
        let p = Promise.race(promises);
        for (let i = 0; i < sequence.length; i++) {
            p = p.then((res) => {
                promises[res] = handler(sequence[i]).then(() => {
                    return res
                });
                return Promise.race(promises)
            })
        }
    })()
}
limitLoad(urls, loadImg, 3)
````
