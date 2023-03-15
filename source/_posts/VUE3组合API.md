---
title: 组合API
date: 2023-03-15 19:15:00
categories: 学习
tags: VUE3

---

###### setup()基本使用
```javascript
    <script> 
            import { ref } feom 'vue'
            const count = ref(0)
            console.log(this.count)
            return {
                count
            }
    </script> 
```
> 这是组合api最基本的用法，ref（）方法是用来定义相应式数据的。记住一定要retun 对象出来才能在模版中使用，才是相应式的。
> 在其中调用可以直接使用this来调用


> 还有一种用法，是使用模版<script setup> 
```javascript
    <script setup> 
            import { ref } feom 'vue'
            const count = ref(0)
            console.log(count.value)
    </script> 
```
> 这是组合api最基本的用法，ref（）方法是用来定义相应式数据的。在这里可以不需要retun因为语法糖已经帮你处理了。
> 在其中调用可以不能使用this来调用，调用的结果是undefined。需要通过.value来调用

###### 访问Props
> 
```javascript
    <script> 
    export default{
        props:{
            title: String
        },
        setup(props){
            console.log(props.title)
        }
    }
         
    </script> 
```
> 你要注意了，props在不解构的情况下是响应式的。一但解构将不再是相应式数据。建议通过props.来调用你的值
> 如果你想要通过解构的方式，需要如下来再次给你的数据加上相应式

```javascript
    <script> 
    import { toRefs,toRef } feom 'vue'
    export default{
        props:{
            title: String
        },
        setup(props){
           const { title } = toRefs(props)
           const title = toRef(props,'title')
        }
    }
         
    </script> 
```
> 那么toRefs和toRef有什么区别呢
>> toRefs是给可枚举的对象添加响应式的，如果是基本类型或者不存在的不可能枚举的建议使用toRef来创建

###### setup上下文
> 传入setup函数的第二个参数是一个setup上下文对象，具体如何上代码
```javascript
    <script> 
    import { toRefs,toRef } feom 'vue'
    export default{
        props:{
            title: String
        },
        setup(props，context){
            // 透传Attributes (非响应式的对象，等价于$attrs)
           console.log(context.attrs)
            // 插槽 (非响应式的对象，等价于$slotts)
           console.log(context.slotts)
            // 触发事件 (非响应式的对象，等价于$emit)
           console.log(context.emit)
            // 暴露公共属性 (非响应式的对象，等价于$expose)
           console.log(context.expose)
        }
    }
         
    </script> 
```
> 那么在setup语法糖<script setup>  </script>  中如何使用的呢。看下面代码!
```javascript
    <script setup> 
    import { defineProps,defineEmits,useAttrs,useSlots } feom 'vue'
    // 获取 props
    const props = defineProps({
        title:String
    })
    // 获取emit 方法，入参是一个数组支持多个输入
   const emit =defineProps(["onFn1","onFn2"])
    // 调用方式
    emit("onFn1",123)    
    emit("onFn2",234)    

    // 获取 $attrs
    const attrs = useAttrs()
    // 获取$slots
    const slots= useSlots()

    </script> 
```
> 在这里特别提醒：我们使用setup语法糖相当于闭包，处理当前的template模版能访问谁度都访问不到包括使用ref访问子组件的方式所以有了如下api
```javascript
    <script setup> 
    import { defineExpose} feom 'vue'
    const a = 1
    const b = 2
    // 这个时候通过 defineExpose将a暴露出去了就能通过ref访问到了，但是b不行
    defineExpose({
        a
    })

    </script> 
```

> 那么我们如何获取DOM元素调用DOM元素呢
>> 1. 语法糖 <p ref="refName"> 调用 const refName = ref() 这样就行了只要保持和你在模版中的命名一致就行
>> 2. 非语法糖就更加简单和vue2 保持一致 可以直接 this.refs.refName
