---
title: vue3源码手写-对象数组跟新
date: 2023-04-10 21:00:00
categories: 源码
tags: VUE3

---
### vue3源码手写-effect实现
###### 1. packages\reactivity\src\effect.ts 
> 第一步在 packages\reactivity\src\effect.ts 创建effect文件，effect中创建trigger做更新处理
```javascript

// 更新
export function trigger(target, type, key?, value?, oldValue?) {
  // 获取一下目标对象，有值那么说明目标对象有对应的依赖收集
  const depsMap = targetMap.get(target);
  // 没有对应的依赖收集就直接retun
  if (!depsMap) return;
  // 利用set集合特性防止有多个相同的改动重复执行
  let effectSet = new Set();
  const add = (effectAdd) => {
    if (effectAdd) {
      effectAdd.array.forEach((element) => {
        // 这里是去重
        effectSet.add(element);
      });
    }
  };

  // 对数组进行特殊处理
  if (key === "length" && isArray(target)) {
    depsMap.forEach((dep, key) => {
      if (key === "length" || key > value) {
        // depsMap.get(key) 这里是拿到对应的目标对象下面的属性下面收集的依赖
        add(dep);
      }
    });
  } else {
    if (key != undefined) {
      // depsMap.get(key) 这里是拿到对应的目标对象下面的属性下面收集的依赖
      add(depsMap.get(key));
    }
    // 数组 修改 索引
    switch(type){
        case TriggerOpTypes.ADD:
            if(isArray(target) && isIntegerKey(key)){
                add(depsMap.get('length'))
            }
    }
  }
  // 执行对应的依赖
  effectSet.forEach((effect: any) => effect());
}
```
###### 2. baseHandlers 依赖收集的地方进行更新操作
> 第二步在 baseHandlers 依赖收集的地方进行更新操作
```javascript
//  代理中的set函数
function createSetter(shallow=false){
    return function set(target,key,value,receiver){
       
        // 判断是数组还是对 是添加值还是修改
        const oldValue = target[key]
        // 判断 是不是数组 值是不是正数 是不是新增 或者 对象的值有没有
        let haskey = isArray(target) && isIntegerKey(key) ? Number(key) <target.length :hasOwn(target,key)
        // 通过映射拿到新的值
        const result = Reflect.set(target,key,value,receiver)
        // 没有对应的值 新增   
        if(!haskey){
                trigger(target,TriggerOpTypes.ADD,key,value)
            }else{
                // 有对应的值就是修改 ，修改的时候要判断是否一致。新值老值
                if(!hasChange(value,oldValue)){
                    // 不一致的时候修改传入新值 value 老值 oldValue
                    trigger(target,TriggerOpTypes.SET,key,value,oldValue)
                }
            }
       
        return result
    }
}

```

###### 创建 operations.ts 文件定义参数
```
export const enum TrackOpTypes {
    GET = 'get',
    HAS = 'has',
    ITERATE = 'iterate'
  }
  
  export const enum TriggerOpTypes {
    SET = 'set',
    ADD = 'add',
    DELETE = 'delete',
    CLEAR = 'clear'
  }
  

```
> 如果需要源码请访问https://github.com/843378894/mini-vue3
