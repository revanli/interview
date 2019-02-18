#### 排序
  [阮一峰排序](https://javascript.ruanyifeng.com/library/sorting.html#toc12)

* 排序的通用函数  
  ```javasctipt
  function swap(i, j, array) {
    var temp = array[j]
    array[j] = array[i]
    array[i] = temp
  }
  ```

* 冒泡排序  
  冒泡排序的原理：从第一个元素开始，把当前元素和下一个索引元素进行比较，如果当前元素大则交换位置，重复操作直到最后一个元素，则此时最后一个元素就是该数组中最大的数。下一轮重复以上操作
  ```javascript
  function bubble(array) {
    for (var i = array.length - 1; i > 0; i++) {
      for (var j = 0; j < i; j++) {
        if (array[j] > array[j+1]) swap(j, j+1, array)
      }
    }
    return array
  }
  ```
  冒泡排序是最容易实现的排序, 最坏的情况是每次都需要交换, 共需遍历并交换将近n²/2次, 时间复杂度为O(n²). 最佳的情况是内循环遍历一次后发现排序是对的, 因此退出循环, 时间复杂度为O(n). 平均来讲, 时间复杂度为O(n²). 由于冒泡排序中只有缓存的temp变量需要内存空间, 因此空间复杂度为常量O(1).
  ![bubble](./image/bubble.gif?raw=true '冒泡排序')


* 插入排序  
  原理：第一个元素默认是已排序元素，取出下一个元素和当前元素比较，如果当前元素大就交换位置，那么此时的第一个元素就是当前的最小数，所以下次取出操作从第三个元素开始，向前对比，重复操作。
  ```javascript
  function insertion(array) {
    for (let i = 1; i < array.length; i++) {
      for (let j = i - 1; j >= 0 && array[j] > array[j+1]; j--) {
        swap(j, j+1, array)
      }
    }
    return array
  }
  ```
  ![insertion](./image/insertion.gif?raw=true '插入排序')

* 选择排序  
  原理：与冒泡排序类似，依次对相邻的数进行两两比较。一轮比较完毕后，找到最大（或最小值）之后，将其放在正确的位置，其他数的位置不变。
  ```javascript
  function selection(array) {
    var len = array.length, min;
    for (i = 0; i < len; i++) {
      min = i

      for (j = i + 1; j < len; j++) {
        if (array[j] < array[min]) {
          min = j
        }
      }

      if (i != min) {
        swap(i, min, array)
      }
    }
    return array
  }
  ```

* 归并排序  
  原理：将两个已经排序的数组合并，要比从头开始排序所有元素来得快。因此，可以将数组拆开，分成n个只有一个元素的数组，然后不断地两两合并，直到全部排序完成。
  ```javascript
  function merge(left, right){
    var result  = [],
        il      = 0,
        ir      = 0;

    while (il < left.length && ir < right.length){
      if (left[il] < right[ir]){
        result.push(left[il++]);
      } else {
        result.push(right[ir++]);
      }
    }

    return result.concat(left.slice(il)).concat(right.slice(ir));
  }

  function mergeSort(myArray){

    if (myArray.length < 2) {
        return myArray;
    }

    var middle = Math.floor(myArray.length / 2),
        left    = myArray.slice(0, middle),
        right   = myArray.slice(middle),
        params = merge(mergeSort(left), mergeSort(right));
    
    // 在返回的数组头部，添加两个元素，第一个是0，第二个是返回的数组长度
    params.unshift(0, myArray.length);

    // splice用来替换数组元素，它接受多个参数，
    // 第一个是开始替换的位置，第二个是需要替换的个数，后面就是所有新加入的元素。
    // 因为splice不接受数组作为参数，所以采用apply的写法。
    // 这一句的意思就是原来的myArray数组替换成排序后的myArray
    myArray.splice.apply(myArray, params);

	  // 返回排序后的数组
    return myArray;
  }
  ```

* 快速排序  
  原理：先确定一个“支点”（pivot），将所有小于“支点”的值都放在该点的左侧，大于“支点”的值都放在该点的右侧，然后对左右两侧不断重复这个过程，直到所有排序完成。
  具体做法：i. 确定“支点”（pivot）。虽然数组中任意一个值都能作为“支点”，但通常是取数组的中间值。   
  ii. 建立两端的指针。左侧的指针指向数组的第一个元素，右侧的指针指向数组的最后一个元素。  
  iii. 左侧指针的当前值与“支点”进行比较，如果小于“支点”则指针向后移动一位，否则指针停在原地。  
  iv. 右侧指针的当前值与“支点”进行比较，如果大于“支点”则指针向前移动一位，否则指针停在原地。  
  v. 左侧指针的位置与右侧指针的位置进行比较，如果前者大于等于后者，则本次排序结束；否则，左侧指针的值与右侧指针的值相交换。  
  对左右两侧重复第2至5步。
  ```javascript
  function partition(myArray, left, right) {

    var pivot   = myArray[Math.floor((right + left) / 2)],
        i       = left,
        j       = right;


    while (i <= j) {

      while (myArray[i] < pivot) {
        i++;
      }

      while (myArray[j] > pivot) {
        j--;
      }

      if (i <= j) {
        swap(myArray, i, j);
        i++;
        j--;
      }
    }

    return i;
  }

  function quickSort(myArray, left, right) {

    if (myArray.length < 2) return myArray;

    left = (typeof left !== "number" ? 0 : left);
    right = (typeof right !== "number" ? myArray.length - 1 : right);

    var index  = partition(myArray, left, right);

    if (left < index - 1) {
      quickSort(myArray, left, index - 1);
    }

    if (index < right) {
      quickSort(myArray, index, right);
    }

    return myArray;
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
