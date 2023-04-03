---
title: vue3源码手写-effect实现
date: 2023-04-03 21:00:00
categories: 源码
tags: VUE3

---
### vue3源码手写-effect实现
###### 1. packages\reactivity\src\effect.ts 
> 第一步在 packages\reactivity\src\effect.ts 创建effect文件，effect做的就是依赖收集对get set put等操作进行收集
```javascript
export {
    reactive,
    readonly,
    shallowReactive,
    shallowReadonly,
  } from './reactive'

```
###### 2. reactive 实现
> 第二步在 对应目录下创建对应的文件（packages\reactivity\src\reactive.ts），内容看代码。
```javascript

export function effect(fun,options:any={}){
const  effect = creatReactEffect(fun,options)
// 不是懒执行 直接执行
    if(!options?.lazy){
        effect()
    }

}
//  定义属性用作区分。每个属性都会有自己的uid
let uid = 0
// 储存当前的effect
let activeEffect
function creatReactEffect(fun,options){
    const effect = function reactEffect(){
        activeEffect=effect
        fun()
    }
    effect.id = uid++; //区分effect
    effect._isEffect = true//区分effect 是不是响应式
    effect.raw = fun//保存传进来的方法
    effect.options = options//保存传进来的属性
    return effect
}

// 收集依赖effect 在获取数据的时候触发get 收集effect
let targetMap= new WeakMap()
export function track(target,type,key){
    // 是undefined则说明没有调用或者不是响应数据
    if(activeEffect === undefined) return 
    // 获取effect {target:dep}
    let depMap = targetMap.get(target)
    if(!depMap){
        // 没有 depMap 说明第一次调用
        targetMap.set(target,(depMap= new Map))
    }
    // 有depMap 就要判断有没有收集属性依赖
    let dep = depMap.get(key)
    if(!dep){
        // 没有收集属性依赖就要添加
        depMap.set(key,(dep= new Set))
    }
    // 有属性要看看依赖收集没有
    if(!dep.has(activeEffect)){
        // 没有要添加
        dep.add(activeEffect)
    }
}

```


