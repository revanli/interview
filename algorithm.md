#### 排序
  [阮一峰排序](https://javascript.ruanyifeng.com/library/sorting.html#toc12)

* 五大算法
  - 贪心算法：局部最优解法
  - 分治算法：分为多个模块，与原问题性质相同
  - 动态规划：每个状态都是过去历史的一个总结
  - 回溯法：发现原先选择不优时，退回重新选择
  - 分支限界法

* 冒泡排序  
  冒泡排序的原理：从第一个元素开始，把当前元素和下一个索引元素进行比较，相邻两个数亮亮对比，如果当前元素大则交换位置，重复操作直到最后一个元素，则此时最后一个元素就是该数组中最大的数。下一轮重复以上操作
  ```javascript
  function bubbleSort(arr) {
    for (let i = arr.length; i >= 2; i--) {
      for (let j = 0; j <= i - 1; j++) {
        if (arr[j] > arr[j + 1]) {
          // 解构
          [arr[j], arr[j + 1]] = [arr[j + 1], arrp[j]]
        }
      }
    }
  }
  ```
  冒泡排序是最容易实现的排序, 最坏的情况是每次都需要交换, 共需遍历并交换将近n²/2次, 时间复杂度为O(n²). 最佳的情况是内循环遍历一次后发现排序是对的, 因此退出循环, 时间复杂度为O(n). 平均来讲, 时间复杂度为O(n²). 由于冒泡排序中只有缓存的temp变量需要内存空间, 因此空间复杂度为常量O(1).
  ![bubble](./image/bubble.gif?raw=true '冒泡排序')


* 插入排序  
  原理：第一个元素默认是已排序元素，取出下一个元素和当前元素比较，如果当前元素大就交换位置，那么此时的第一个元素就是当前的最小数，所以下次取出操作从第三个元素开始，向前对比，重复操作。简单来说就是将元素插入到已排序好的数组中
  ```javascript
  function insertion(arr) {
    for (let i = 1; i < arr.length; i++) {  // 外循环从1开始，默认arr[0]是有序段
      for (let j = i; j > 0; j--) {
        if (arr[j] < arr[j - 1]) {
          [arr[j], arr[j - 1]] = [arr[j - 1], arr[j]]
        } else {
          break
        }
      }
    }
  }
  ```
  ![insertion](./image/insertion.gif?raw=true '插入排序')

* 选择排序  
  原理：默认第一个元素是已经排好序的元素，遍历其他元素和它对比，一轮比较完毕后，找到最大（或最小值）之后，将其与第一个值交换，其他数的位置不变。
  ```javascript
  function selection(arr) {
    var len = arr.length, min;
    for (i = 0; i < len; i++) {
      // 默认第一个元素是已经排好序的元素，记录最小值的下标
      min = i

      for (j = i + 1; j < len; j++) {
        if (arr[j] < arr[min]) {
          min = j
        }
      }

      if (i != min) {
        [arr[min], arr[i]] = [arr[i], arr[min]]
      }
    }
    return array
  }
  ```

* 快速排序  
  - 选择基准值(base), 原数组长度减一(基准值), 使用splice
  - 循环原数组, 小的放左边(left数组), 大的放右边(right数组)
  - concat(left, right)
  - 递归继续排序left与right
  ```javascript
  function quickSort(arr) {
    if (arr.length <= 1) {
      return arr  // 递归出口
    }

    var left = [], right = [], current = arr.splice(0, 1) // 取第一个为基准
    for (let i = 0; i < arr.length; i++) {
      if (arr[i] < current) {
        left.push(arr[i])  // 放在左边
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
    return arr.length <= 1 ? arr : quickSort(arr.slice(1).filter(item => item <= arr[0])).concat(arr[0], quickSort(arr.slice(1).filter(item => item > arr[0])))
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


