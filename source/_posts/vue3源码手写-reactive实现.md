---
title: vue3源码手写-reactive实现
date: 2023-03-16 22:00:00
categories: 源码
tags: VUE3

---
### vue3源码手写-reactive实现
###### 1. packages\reactivity\src\index.ts 安装源码该位置做入口，只做导入导出
> 第一步在 packages\reactivity\src\index.ts 下导入导出我们在reactive.ts的方法
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

// shared 中写的是公共代码 需要先npm run build 打包才能引入，build报错的话先执行yarn install 再build 
import { isObject} from'@vue/shared'

const reactiveHandlers ={}
const shallowReactiveHandlers ={}
const readonlyHandlers ={}
const shallowReadonlyHandlers ={}

/**
 * vue3核心方法实现
 * reactive  创建响应式数据
 * shallowReactive 第一层的响应式
 * readonly 只读方法
 * readonly浅层的只读
 * 
 */
export function reactive(target){

    return createReactObj(target,false,reactiveHandlers)

}

export function shallowReactive(target){
    return createReactObj(target,false,shallowReactiveHandlers)
    
}

export function readonly(target){
    return createReactObj(target,true,readonlyHandlers)
    
}

export function shallowReadonly(target){
    return createReactObj(target,true,shallowReadonlyHandlers)
    
}
/**
 * weakMap  相当于Map 但是key是对象 而且有自动垃圾回收
 * 使用weakMap 储存代理了的target是性能优化，防止已经代理过的数据多次代理
 * 
 */
const reactiveMap = new WeakMap()
const readonlyMap = new WeakMap()



/**
 * 高阶函数根据入参处理不同的业务群相同业务又能共用
 * 
 * @description 代理核心方法
 * 
 * @target 目标对象，创建响应式传入的对象
 * 
 * @isReadonly 是否只读
 * 
 * @baseHandlers  代理捕获核心  相当于Objeact 的get set 等捕获设置
 */
function   createReactObj(target,isReadonly,baseHandlers){
    if(!isObject(target)){
        // 如果不是对象就直接返回 proxy只能代理对象
        return target
    }
// 根据是否只读来区分使用map集合储存
    const proxymap = isReadonly ? readonlyMap : reactiveMap
    // 取一下数据看集合中是否有该对象
    const proxyEs = proxymap.get(target)
    // 在集合中表明已经代理过了，直接返回
    if(proxyEs){
        return proxyEs
    }
    const proxy = new Proxy(target,baseHandlers)
    proxymap.set(target,proxy)
    return proxy
}

```

###### 3. baseHandlers 代理捕获器对象书写
> 第三步 先对应目录下创建文件 （packages\reactivity\src\baseHandlers.ts）。实现看代码
```javascript
    import { isObject } from "@vue/shared"
import { reactive, readonly } from "./reactive"

/**
 * 该文件写的是代理的捕获配置对象不了解的话看一下ES6的Proxy ;new Proxy(target,{get:function,set:function,delete:function})
 * @description 注意这里是对象不是方法 
 * 
 * 
 */
const get = /*#__PURE__*/ createGetter()
const shallowGet = /*#__PURE__*/ createGetter(false, true)
const readonlyGet = /*#__PURE__*/ createGetter(true)
const shallowReadonlyGet = /*#__PURE__*/ createGetter(true, true)


function createGetter(isReadonly=false,shallow=false){
    return function get(target,key,receiver){
        // Reflect.get 相当于target[key] 好处是这个方法不会直接操作原对象{a:{b:{}}} res = {b:{}}
        const res = Reflect.get(target,key,receiver)
        if(!isReadonly){
            // 不是只读操作 要做依赖收集
        }

        if(shallow){
            //  浅层代理直接返回 之前在reactve中已经代理过一次了
            return res
        }

        if(isObject(res)){
            // 这里判断那到的value还是对象 我们在外面使用了 target.a 会触发get 方法走进createGetter 现在的res = {b:{}}
            // 递归调用代理方法
            // 遇到的面试题:为什么使用proxy性能根据好，因为Objeact.definepropty 上来就是递归处理，proxy是懒代理只有在调用的时候才会去递归代理
            return isReadonly ? readonly(res) : reactive(res)

        }
        return res
    }

}

export const reactiveHandlers ={
    get
}
export const shallowReactiveHandlers ={}
export const readonlyHandlers ={}
export const shallowReadonlyHandlers ={}
```



> 我们才开了个头，让我们慢慢的一起完善把。