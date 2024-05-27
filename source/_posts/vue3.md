---
title: vue3
date: 2024-05-23 23:02:01
tags:
---
### ref,reactive响应式原理和区别
ref和reactive都是用来创建响应式数据的，但是它们之间有一些区别。

ref用来创建一个响应式的引用，可以用来包装基本数据类型，例如字符串、数字、布尔值等。reactive用来创建一个响应式对象或数组，可以用来包装复杂的数据结构。

ref创建的响应式引用，可以通过.value来访问和修改其值。reactive创建的响应式对象或数组，可以直接访问和修改其属性。

原理可以简单说明为，porxy代理的对象必须是一个对象，所以针对基本类型转化为proxy({value: xxx}) 所以需要.value来访问，而reactive是主要用作复杂类型会是一个对象所以不需要value

### toRefs,toRef的作用和区别
toRefs是将一个响应式对象的所有属性转换为普通对象，并返回一个新的对象，其中每个属性都是一个ref，指向原始对象的相应属性。toRef的作用是将一个响应式对象的属性转换为ref，从而可以在模板中直接使用(当原对象不存在的属性没有绑定时，可以通过toRef来绑定,类似vue2的$set)。

```vue
<template>
  <div>
    <p>{{ foo }}</p>
    <p>{{ bar }}</p>
    <p>{{ age }}</p>
  </div>
</template>

<script>
export default {
  setup() {
    const state = reactive({
      foo: 1,
      bar: 2
    })
    // toRefs(proxy({foo:1,bar:2}))  => {foo:ref(1),bar:ref(2)}
    const {foo, bar} = toRefs(state)
    const age = toRef(state, 'age')
    return {
      foo,
      bar,
      age
    }
  }
}
</script>
```
### setup
setup是一个新的组件选项，用于在组件中使用Composition API。setup会在组件的beforeCreate生命周期钩子之前被调用（由于是在实例生成前调用所以获取不到this的vue实例），并且可以访问组件的props和context。setup返回的对象中的属性会暴露在模板中，并且可以在组件的整个生命周期中使用。
props -> defineProps
context -> defineContext
emit -> defineEmits
slots -> defineSlots -> useSlots
attrs -> defineAttrs -> useAttrs

```js
<script setup>
import { useSlots, useAttrs } from 'vue'

const slots = useSlots()
const attrs = useAttrs()

const props = defineProps({
  foo: String
})

const emit = defineEmits(['change', 'delete'])
</script>
```

```js
const app = Vue.createApp({
  template: `
    <child :name="'child'">hello</child>
    `
})
app.component('child', {
  setup(props, context) {
    console.log(props) // {name: 'child'}
    const { attrs, slots, emit} = context
    console.log(context) // {attrs: {}, slots: {default: [Function: child]}, emit: [Function: emit]}
  }
})
```
### 计算属性 computed
```js
const app = Vue.createApp({
  setup() {
    const state = reactive({
      firstName: 'John',
      lastName: 'Doe'
    })
    
    const fullName = computed(() => {
      return state.firstName + ' ' + state.lastName
    })
    const fullName2 = computed({
      get() {
        return state.firstName + ' ' + state.lastName
      },
      set(newValue) {
        const [firstName, lastName] = newValue.split(' ')
        state.firstName = firstName
        state.lastName = lastName
      }
    })
    setTimeout(() => {
      // set 
      fullName2.value = 'Jane Doe'
    }, 1000)
    
    return {
      fullName,
      fullName2
    }
  }
})
```

### watch 和 watchEffect
watch用来监听响应式数据的变化，惰性的，可获取当前值，原始值，可监听多个响应式数据的变化。
而watchEffect则是在副作用发生时(即传递的回调函数中的响应式数据发生变化)自动触发监听器，会立即执行。
取消监听都是调用watch，watchEffect的返回值
```js
const app = Vue.createApp({
  setup() {
    const state = reactive({
      firstName: 'John',
      lastName: 'Doe'
    })
    
    const al = watchEffect(() => {
      console.log(state.firstName + ' ' + state.lastName)
      setTimeout(() => {
       al();// 取消监听
      }, 1000)
    })
    
    // 惰性的（可通过immidiate）
    const b1 = watch([() => state.firstName, () => state.lastName], (newValue, oldValue) => {
      console.log('firstName:new-old_'+newValue[0] + '-' + oldValue[0],'lastName:new-old_'+newValue[1]+'-'+oldValue[1])
      setTimeout(() => {
       b1();// 取消监听
      }, 1000)
    },{
      // 立即执行
      immediate:true 
      })
  
})
```

### 生命周期
beforeCreate -> created -> beforeMount -> mounted -> beforeUpdate -> updated -> beforeUnmount -> unmounted

### 新的生命周期
onBeforeMount -> onMounted -> onBeforeUpdate -> onUpdated -> onBeforeUnmount -> onUnmounted -> onErrorCaptured
onRenderTracked(收集依赖) -> onRenderTriggered(页面触发渲染)

### readonly,shallowReadonly的作用和区别
readonly和shallowReadonly都是用来创建一个只读的响应式数据，但是它们之间有一些区别。

readonly创建的响应式数据，只读，不能被修改。shallowReadonly创建的响应式数据，只读，但是不会递归处理对象的子属性。


### shallowReactive,shallowRef的作用和区别
shallowReactive和shallowRef是用来创建浅层响应式数据的。浅层响应式数据是指只处理对象的第一层属性，而不会递归处理对象的子属性。

shallowReactive创建的响应式对象，只处理对象的第一层属性，而不会递归处理对象的子属性。shallowRef创建的响应式引用，只处理引用类型的第一层属性，而不会递归处理引用类型的子属性。


```
