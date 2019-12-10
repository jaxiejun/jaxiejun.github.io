## 深度克隆

<p>需要的检测数据类型函数</p>

```js
function isArray(origin) {
    return origin instanceof Array;
}
function isObject(origin){
    return typeof origin === 'object' && origin.toString() === '[object Object]';
}
```

<p>递归实现</p>

```js
//ES5可行 未考虑 new String , new Boolean , Map , Set
function deepCopyRecursion(origin) {
    if(isObject(origin) || isArray(origin)){
        var target = isObject(origin) ? {} : [];
        if(Object.keys(origin).length === 0) return target;
        for(var prop in origin){
            //func instanceof Object ==> true //不影响
            if(origin instanceof Object){
                target[prop] = arguments.callee(origin[prop]);
            }else{
                target[prop] = origin[prop];
            }
        }
        return target;
    }else{
        return origin;
    }
}
```

<p>非递归(迭代)实现</p>

```js
//未完成
function deepCopyInterate(origin) {
    //同样判断是基础类型还是引用类型
    if(isObject(origin) || isArray(origin)){
        //to do
        var target = isObject(origin) ? {} : [];
        //数组保存队列
        var currentArr = [origin];
        while(currentArr.length){
            var q = currentArr.shift();
            for(var prop in origin){
                if(origin instanceof Object){
                    currentArr.push(origin[prop])
                    // target[prop] = arguments.callee();
                }else{
                    target[prop] = origin[prop];
                }
            }
        }
    }else{
        return origin
    }
}
```