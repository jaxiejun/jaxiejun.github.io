## 冒泡排序
<i>相邻的比较,大的靠后,一直到最后两个,此时最大的在最后。如此循环n - 1次</i>

```javascript
function bubblesort(arr) {
    for( i = arr.length ; i >= 2; i --){
        for(var j = 0; j < i; j ++){
            if(arr[j] > arr[j + 1]){
                [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
            }
        }
    }
    return arr;
}
```

## 选择排序
<i>每一次取第i个, 和后面每个进行比较,大于则互换,直到倒数第二个和最后一个的比较时结束</i>

```javascript
function selectSort(arr) {
    for(var i = 0, len = arr.length; i < len; i ++){
        for(var j = i + 1; j < len; j ++){
            if(arr[i] > arr[j]){
                [arr[i], arr[j]] = [arr[j], arr[i]]
            }
        }
    }
    return arr;
}
```

## 插入排序
<i>i个数组成已排序数组, 将第 i + 1个插入前面, 若大于前一个, 则j位后移一位，一直到比较元素大于前面的数</i>

```javascript
function insertSort(arr) {
    var temp = 0, j = 0;
    for(var len = arr.length, i = 1; i <  len; i ++){
        temp = arr[i];
        j = i ;
        while(j > 0 && arr[j - 1] > temp){
            arr[j] = arr[j - 1];
            j --;
        }
        arr[j] = temp;
    }
    return arr;
}
```