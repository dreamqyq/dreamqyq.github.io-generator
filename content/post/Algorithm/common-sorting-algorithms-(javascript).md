---
title: "常见的排序算法（JavaScript实现）"
date: 2018-09-17
draft: false
tags: ["Algorithm"]
categories: ["Algorithm"] 
---
## 冒泡排序 —— O(n^2)

```js
function bubleSort(arr) {
  for(let i = 0; i < arr.length-1 /*i代表轮数*/; i++) {
    for(let j = 0; j < arr.length - 1 - i /*j代表当前轮选中的元素下标*/; j++) {
      if(arr[j] > arr[j+1]) {
        [ arr[j], arr[j+1] ] = [ arr[j+1], arr[j] ] /*交换元素*/
      }
      //console.log(arr)
    }
  }
}

var arr = [10, 34, 21, 47, 3, 28]
bubleSort(arr)
console.log(arr)
```

## 选择排序 —— O(n^2)

```js
function sectionSort(arr) {
  for(let min = i = 0; i < arr.length - 1 /*i代表轮数*/; i++) {
    min = i
    for(let j = i + 1; j < arr.length; j++) {
      if(arr[min] > arr[j]) {
        min = j
      }
    }
    [ arr[i], arr[min] ] = [ arr[min], arr[i] ] //把每轮的第一个和当前轮的最小值交换位置
  }
}

var arr = [10, 34, 21, 47, 3, 28]
sectionSort(arr)
console.log(arr)
```

## 插入排序 —— O(n^2)
- 普通版
```js
function insertionSort(arr) {
  for(var i = 1; i < arr.length; i++) {
    var temp = arr[i]
    for(var j = i; j > 0 && arr[j-1] > temp; j--) {
      arr[j] = arr[j-1]
    }
    arr[j] = temp
  }
}


var arr = [10, 34, 21, 47, 3, 28]
insertionSort(arr)
console.log(arr)
```
- JS版
```js
function insertionSort(arr) {
  for(let i = 1; i < arr.length; i++) {
    for(let j = 0; j < i; j++) {
      if(arr[i] < arr[j]) {
        arr.splice(j, 0, arr[i])
        arr.splice(i+1, 1)
        break
      }
      console.log(arr)
    }
  }
}

var arr = [10, 34, 21, 47, 3, 28]
insertionSort(arr)
console.log(arr)
```

## 快速排序 ——  O(nlogn)
- 普通版（存在空间浪费）
```js
unction quickSort(arr) {
  if(arr.length <= 1) {
    return arr
  }
  let leftArr = [] 
  let rightArr = []
  for(let i = 1; i < arr.length; i++) {
    if(arr[i] >= arr[0]) {
      rightArr.push(arr[i])
    } else {
      leftArr.push(arr[i])
    }
  }
  return quickSort(leftArr).concat(arr[0]).concat(quickSort(rightArr))
}

var arr = [10, 34, 21, 47, 3, 28]
quickSort(arr)
console.log(arr)
```
- 优化版（在原数组上操作）
```js
function quickSort(arr) {
  function _quickSort(arr, start, end) {
    if(start >= end) return
    let key = arr[end]
    let left = start, right = end - 1
    while(left < right) {
      while(arr[left] < key && left < right) left++
      while(arr[right] >= key && left < right) right--
      [arr[left], arr[right]] = [arr[right], arr[left]]
    }
    if(arr[left] >= arr[end]) { 
      [arr[left], arr[end]] = [arr[end], arr[left]]
    } else {  // 如 [2, 1, 3, 4]
      left++
    }
    _quickSort(arr, start, left - 1)
    _quickSort(arr, left + 1, end)
  }
  _quickSort(arr, 0, arr.length - 1)
  return arr
}
```

## 归并排序 ——  O(nlogn)

```js
/*
left : [1, 3, 4, 7]
right: [2, 5, 6, 9]
result: [1, 2, 3, 4, 5, 6, 7, 9]
 */

function mergeSort(arr) {
  var merge = function(left, right) {
    var result = []
    while(left.length && right.length) {
      result.push(left[0] <= right[0] ? left.shift() : right.shift())
    }
    return result.concat(left.concat(right))
  }
  if(arr.length < 2) return arr
  var mid = arr.length >> 1
  return merge(mergeSort(this.slice(0, mid)), mergeSort(this.slice(mid)))
}
```

## 希尔排序 —— O(nlog2n)

```js
function shellSort(arr) {
  var temp
  var len = arr.length
  for (var gap = len >> 1; gap > 0; gap = gap >>=1) {
    for (var i = gap; i < len; i++) {
      temp = arr[i]
      for( j = i - gap; j >=0 && arr[j] > temp; j -= gap) {
        arr[j + gap] = arr[j]
      }
      arr[j + gap] = temp
    }
    console.log(arr)
  }
}

var arr = [3, 1, 7, 2, 5, 0, 4, 6]
shellSort(arr)
console.log(arr)
```
