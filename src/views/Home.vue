<template>
  <h1>Home</h1>
  <h2>ref: {{ num }}</h2>
  <h2>reactive: {{ state.count }}</h2>
  <h2>computed: {{ num1 }}</h2>
  <h2>可修改的computed: {{ num2.value }}</h2>
  <h2>watch监听多个: {{ num.value }} {{state.name}}</h2>
</template>
<script>
import { ref, reactive, readonly, computed, watch } from "vue";
export default {
  // 可以理解为是组件的入口 在beforeCreate之前调用 函数返回的内容会作为模板渲染上下文
  setup() {
    // ref函数接一个参数返回一个响应式的ref对象;值得注意的是：ref返回的是一个对象，需要通过它的value属性拿到它的值,也即num.value
    const num = ref(2);
    num.value += 3;

    // reactive 与 vue.observerable一样 将数据变成响应式
    const state = reactive({
      count: 5,
      name: "张三",
    });

    // readonly这个函数，参数可以一个响应式或者普通的对象再或者是个ref。返回的是一个只读代理（深层的）
    const obj = readonly(state);
    obj.count = 20; // Set operation on key "count" failed: target is readonly

    // computed是一个函数，它需要传一个getter函数。其返回值是一个不可手动修改的ref对象
    const num1 = computed(() => num.value + 1);
    num1.value++; //Write operation failed: computed value is readonly
    // 想要一个可以进行修改的，就需传一个具有get和set函数的对象
    const num2 = computed({
      get: () => num,
      set: (value) => (num.value = value),
    });
    num2.value = 3;

    //watch 监听一个
    watch(
      () => state.count,
      (count, preCount) => {
        console.log(`new ${count} --- old ${preCount}`);
      }
    );
    setTimeout(() => {
      state.count = 23;
    }, 2000);
    // watch 监听多个，即如下只要num或state有一个发生变动就触发监听器处理回调
    watch([num, () => state.name], ([newNum, newName], [oldNum, oldName]) => {
      console.log(`new ${newNum},${newName}---old ${oldNum},${oldName}`);
    });
    setTimeout(() => {
      num.value = 6;
      state.name = "李四";
    }, 2000);

    return { num, state, num1, num2 };
  },
};
</script>
<style lang="less">
</style>