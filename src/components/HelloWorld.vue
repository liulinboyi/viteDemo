<template>
  <h1>{{ msg }}</h1>
  <h2>{{object.foo}}</h2>
  <button @click="count++">count is: {{ count }}</button>
  <button @click="id++">id is: {{ id }}</button>
  <p>Edit <code>components/HelloWorld.vue</code> to test hot module replacement.</p>
</template>

<script>
import { ref, reactive, watchEffect } from 'vue'
export default {
  setup() {
    const count = ref(0)
    const id = ref(1)
    const object = reactive({ foo: 'bar' })

    // watchEffect(() => console.log(count.value))
    // -> 打印出 0

    // setTimeout(() => {
    //   count.value++
    //   // -> 打印出 1
    // }, 100)

    // cancel
    function cancelable(promise) {
      let cancel = false;
      let p = new Promise((resolve, reject) => {
        promise.then(val => {
          if (cancel) {
            reject({ reason: 'cancelled' })
          }
          resolve(val)
        })
      })
      // let p = promise.then(val => {
      //   if (cancel) {
      //     // throw cancelable.cancel_error;
      //     console.log('prev is cancel')
      //     Promise.reject()
      //   }
      //   return val;
      // });
      p.cancel = () => cancel = true;
      return p;
    }
    // cancelable.cancel_error = new Error('promise canceled.');


    function sleep(time) {
      return new Promise(resolve => setTimeout(() => { resolve() }, time))
    }


    function performAsyncOperation(id) {
      let sl = sleep(5000)
      console.log(sl, 'sl');
      return cancelable(sl)
    }

    watchEffect((onInvalidate) => {
      const token = performAsyncOperation(id.value)
      token.then(() => { console.log('success') })
      console.log(token, 'token');
      onInvalidate(() => {
        console.log("cancel");
        // id 改变时 或 停止侦听时
        // 取消之前的异步操作
        token.cancel()
      })
    })

    // 暴露给模板
    return {
      count,
      object,
      id
    }
  },
  name: 'HelloWorld',
  props: {
    msg: String
  },
  // data() {
  //   return {
  //     count: 0
  //   }
  // }
}
</script>
