---
layout:     post
title:      "serialize-javascript 源码剖析"
subtitle:   "代码剖析"
date:       2018-03-28
author:     "Memotail"
catalog:    true
tags:
    - 代码剖析
---

# serialize-javascript

## 基本信息
- version: 1.4.0
- 总行数：117（含注释）
- github：https://github.com/yahoo/serialize-javascript


### 提供了什么能力

1. 对比`JSON.stringify(json)`，不仅仅序列化json，还支持function/regexp/date格式数据；
2. 防止xss，转译了`< > /  \u2028(换行符) \u2029(段落分隔符)`字符 


### 能用来干什么

输出json到html页面使用，如：配置文件、同构的初始化的state等


## 源码剖析

若显示说明是简单的json格式（不含function、regexp、date），则直接使用原生能力（官方说会快3倍）。

```javascript
if (options.isJSON && !options.space) {
    str = JSON.stringify(obj);
} else {
    str = JSON.stringify(obj, options.isJSON ? null : replacer, options.space);
}
```
> JSON.stringify第二个参数，主要用于遍历数据进行调整


若不是简单json格式，则使用迭代数据，将特殊数据格式进行数据占位替换，并将value存到指定数组里面，用于后续操作（先做xss更改，再恢复数据）

```javascript
function replacer(key, value) {
    if (!value) {
        return value;
    }
    
    // 通过 this[key] 获取每个值，判断特殊格式，将特殊格式的值保存起来，添加占位符
    var origValue = this[key];
    var type = typeof origValue;

    if (type === 'object') {
        if(origValue instanceof RegExp) {
            return '@__R-' + UID + '-' + (regexps.push(origValue) - 1) + '__@';
        }

        if(origValue instanceof Date) {
            return '@__D-' + UID + '-' + (dates.push(origValue) - 1) + '__@';
        }
    }

    if (type === 'function') {
        return '@__F-' + UID + '-' + (functions.push(origValue) - 1) + '__@';
    }

    return value;
}

```

进行xss防注入替换，将不安全的html以及无效的js的终结符
```javascript
str = str.replace(UNSAFE_CHARS_REGEXP, escapeUnsafeChars);
```

将临时保存的特殊数据类型数据转化为字符串，并替换回指定位置

```javascript
str.replace(PLACE_HOLDER_REGEXP, function (match, type, valueIndex) {
    if (type === 'D') {
        return "new Date(\"" + dates[valueIndex].toISOString() + "\")";
    }

    if (type === 'R') {
        return regexps[valueIndex].toString();
    }

    var fn           = functions[valueIndex];
    var serializedFn = fn.toString();

    if (IS_NATIVE_CODE_REGEXP.test(serializedFn)) {
        throw new TypeError('Serializing native function: ' + fn.name);
    }

    return serializedFn;
});
```
