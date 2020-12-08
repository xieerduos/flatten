### 以下代码在 node 执行

```bash
$ node -v
v14.11.0
```

### 5 种方式实现数组扁平化

数组扁平化 概念

数组扁平化是指将一个多维数组变为一维数组;

[1, [2, 3, [4, 5]]] ===> [1, 2, 3, 4, 5]

### 代码实现

```js
const source1 = [1, [2, 3, [4, 5]]];
const source2 = [{a: 1}, [{b: 1}, [{c: 1}]]];
```

### 1. reduce

遍历数组每一项, 若值为数组继续递归遍历,否则 concat

```js
// 1. reduce
// 遍历数组每一项, 若值为数组继续递归遍历,否则 concat
function flatten(arr) {
    return arr.reduce((result, item) => {
        // [].concat([1, 2]) === [1, 2]
        // [1].concat(2, 3) === [1, 2, 3]
        return result.concat(Array.isArray(item) ? flatten(item) : item);
    }, []);
}

console.log('flatten(source1)', flatten(source1));
console.log('flatten(source2)', flatten(source2));
// flatten(source1) [ 1, 2, 3, 4, 5 ]
// flatten(source2) [ { a: 1 }, { b: 1 }, { c: 1 } ]
```

### 2. toString & split

调用数组的 toString 方法,将数组变为字符串然后再用 split 分割还原为数组

这种方式如果仅仅是 `['1', ['2']]` 像这样的简单的数组可以，

复杂数据类型就会有错误

```js
function flatten2(arr) {
    return arr.toString().split(',');
    // const result = arr.toString().split(',');
    // return result.map((item) => ~~item);
}

console.log('flatten2(source1)', flatten2(source1));
console.log('flatten2(source2)', flatten2(source2));
// flatten2(source1) [ '1', '2', '3', '4', '5' ] (bug)
// flatten2(source2) [ '[object Object]', '[object Object]', '[object Object]' ] (bug)
```

### 3. join & split

和上面的 toString 一样,join 也可以将数组转换为字符串

```js
// 和上面的toString一样,join 也可以将数组转换为字符串

function flatten3(arr) {
    return arr.join(',').split(',');
    // return arr.join(',').split(',').map(i => ~~item);
}

console.log('flatten3(source1)', flatten3(source1));
console.log('flatten3(source2)', flatten3(source2));
// flatten3(source1) [ '1', '2', '3', '4', '5' ] (bug)
// flatten3(source2) [ '[object Object]', '[object Object]', '[object Object]' ] (bug)
```

### 4. 递归 & forEach

本质上和 1. 一样

```js
// 4. 递归

function flatten4(arr) {
    let result = [];
    arr.forEach((item) => {
        if (Array.isArray(item)) {
            result = result.concat(flatten4(item));
        } else {
            result.push(item);
        }
    });
    return result;
}

console.log('flatten4(source1)', flatten4(source1));
console.log('flatten4(source2)', flatten4(source2));
// flatten4(source1) [ 1, 2, 3, 4, 5 ]
// flatten4(source2) [ { a: 1 }, { b: 1 }, { c: 1 } ]
```

### 5. 可扩展运算符

es6 的扩展运算符能将二维数组变为一维数组

```js
// 原理：
// [].concat(...[1, 2, 3, [4, 5]])
// (5) [1, 2, 3, 4, 5]

function flatten5(arr) {
    while (arr.some((item) => Array.isArray(item))) {
        arr = [].concat(...arr);
    }
    return arr;
}

console.log('flatten5(source1)', flatten5(source1));
console.log('flatten5(source2)', flatten5(source2));
// flatten5(source1) [ 1, 2, 3, 4, 5 ]
// flatten5(source2) [ { a: 1 }, { b: 1 }, { c: 1 } ]
```
