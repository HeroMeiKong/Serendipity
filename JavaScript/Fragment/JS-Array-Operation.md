# JavaScript 数组操作

## 1. 去掉重复的数组元素

  ```javascript
  Array.prototype.unique = function () {
    let arr = [];
    let obj = {};
    for (let i = 0, length = this.length; i < length; i++) {
      if (!obj[this[i]]) {
        arr.push(this[i]);
        obj[this[i]] = this[i];
      }
    }
    return arr;
  }

  let arr = [1, 2, 3, 1, 1, 3, 3, 4, 5, 6, 5, 5, 5, 5, 7, 8, 9, 10, 9, 9, 9];
  let unique = arr.unique();

  console.log(unique); // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
  ```

## 2. 获取一个数组中的重复项

  ```javascript
  Array.prototype.delete = function () {
    let arr = this;
    let obj = {};
    let delArr = [];
    for (let i = 0, length = arr.length; i < length; i++) {
      let key = arr[i];
      if (typeof obj[key] === 'undefined') {
        obj[key] = "1";
        i++;
        continue;
      }
      delArr.push(arr.splice(i, 1)[0]);
      let l = arr.length;
    }
    return delArr;
  }

  let arr = [1, 2, 3, 1, 1, 3, 3, 4, 5, 6, 5, 5, 5, 5, 7, 8, 9, 10, 9, 9, 9];
  let delArr = arr.delete();
  console.log(delArr); //[1, 1, 3, 3, 5, 5, 5, 5, 9, 9, 9]
  ```

## 3. 求一个字符串的字节长度，一个英文字符占用一个字节，一个中文字符占用两个字节

  ```javascript
  !(function getByte(str) {
    let num = 0;
    for (let i = 0, length = str.length; i < length; i++) {
      if (str.charCodeAt(i) > 255) {
        num += 2;
      } else {
        num++;
      }
    }
    console.log(num);
  })("你好，John!");
  ```

## 4. 判断一个字符串中出现次数最多的字符，统计这个次数

  ```javascript
  //将字符串的字符保存在一个hash table中，key是字符，value是这个字符出现的次数
  let str = "abcdefgaddda";
  let obj = {};
  for (let i = 0, length = str.length; i < length; i++) {
    let key = str[i];
    if (typeof obj[key] === 'undefined') {
      obj[key] = 1;
    } else {
      obj[key]++;
    }
  }
  /*遍历这个hash table，获取value最大的key和value*/
  let max = -1;
  let max_key = "";
  for (let key in obj) {
    if (max < obj[key]) {
      max = obj[key];
      max_key = key;
    }
  }
  console.log("max: " + max + ",max_key: " + max_key);
  ```

## 5. 数组排序(支持 number & string)

  ```javascript
  function compareNumbers(a, b) {
    return a - b;
  }

  let arr = [13, 34, 64, 12, 1, 5, 980, 3, 1000]
  console.log(arr.sort(compareNumbers))
  ```

## 6. 快速排序

  ```javascript
  //复杂度O(nlog2n) 最差O(n^2)

  function quickSort(arr) {

    //如果数组只有一个数，就直接返回；
    if (arr.length <= 1) {
      return arr;
    }

    //找到中间的那个数的索引值；如果是浮点数，就向下取整
    let centerIndex = Math.floor(arr.length / 2);

    //根据这个中间的数的索引值，找到这个数的值；
    let centerNum = arr.splice(centerIndex, 1);

    //存放左边的数
    let arrLeft = [];
    //存放右边的数
    let arrRight = [];

    for (let i = 0, length = arr.length; i < length; i++) {
      if (arr[i] < centerNum) {
        arrLeft.push(arr[i]);
      } else {
        arrRight.push(arr[i]);
      }
    }
    return quickSort(arrLeft).concat(centerNum,quickSort(arrRight));
  };
  let arrSort = [33, 18, 2, 40, 16, 63, 27, 16, 63];
  let newArr = quickSort(arrSort);
  console.log(newArr);
  ```

## 7. 数组随机顺序输出

  ```javascript
  function randomOut(arr) {
    return arr.sort(function () { return Math.random() > 0.5 ? -1 : 1; })
  }
  randomOut([1, 2, 3, 4, 5])
  ```

## 8. 判断是否为数组

  ```javascript
  let is_array = function (value) {
    return value && typeof value === 'object' &&
      value.constructor === Array &&
      typeof value.length === 'number' &&
      typeof value.splice === 'function' &&
      !(value.propertyIsEnumerable('length'));
    }

  //方法1
  arr instanceof Array
  //方法2
  function isArray(o) { return Object.prototype.toString.call(o) === '[object Array]'; }
  //方法3
  arr.constructor === Array && typeof arr === "object"

  //通用判断类型函数
  function is(type, obj) {
    let clas = Object.prototype.toString.call(obj).slice(8, -1);
    return obj !== undefined && obj !== null && clas === type;
  }


  JavaScript 类型表格

  Value                      Class      Type
  --------------------------------------------
  new String("foo")          String     object
  new Number(1.2)            Number     object
  new Boolean(true)          Boolean    object
  new Date()                 Date       object
  new Error()                Error      object
  [1,2,3]                    Array      object
  new Array(1, 2, 3)         Array      object
  new Function("")           Function   function
  /abc/g                     RegExp     object (function in Nitro/V8)
  new RegExp("meow")         RegExp     object (function in Nitro/V8)
  {}                         Object     object
  new Object()               Object     object
  ```

## 9. 有一个int型数组，里面有若干数字。要求统计出一共有多少种不同的数字？每种数字出现的频率从少到多排列，频率相同则从小到大排列

  ```javascript
  let arr = [31, 31, 32, 13, 42, 5, 42, 8, 9, 6, 7, 7, 3, 4, 51, 8, 9, 0, 2, 4, 6];
  newArr(arr);

  function newArr(arr) {
    arr.sort(function(a, b) { return (a - b) });

    let length = arr.length;
    let newarr = [];
    let n = 1;

    for (let i = 0; i < length; i++) {
      if (arr[i] === arr[i+1]) {
        n++;
      } else {
        newarr.push({
          value: arr[i],
          number: n
        });
        n = 1;
      }
    }

    let compare = function (x, y) {
      if ((x.number > y.number) || (x.number === y.number && x.value > y.value)) return 1;
      if ((x.number < y.number) || (x.number === y.number && x.value < y.value )) return -1;
    }

    newarr.sort(compare);
    let ints = [];
    for (let i = 0, len = newarr.length; i < len; i++) {
      ints.push(newarr[i].value)
    }
    console.log(ints); /* ints就是最后结果 */
  }
  ```
