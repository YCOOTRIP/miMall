<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'App',
  created() {
    // 初始化完毕后 立即获取数据库中数据保存到vuex中 用于渲染NavHeader组件的用户名和购物车数量
    if (this.$cookie.get('userId')) {
      // 只有处于登录状态才去请求用户数据
      this.getUser()
      this.getCartCount()
    }
  },
  methods: {
    getUser() {
      this.axios.get('/user').then((res = {}) => {
        // res 默认值为空 防止未登录时报错
        this.$store.dispatch('saveUserName', res.username)
      })
    },
    getCartCount() {
      this.axios.get('/carts/products/sum').then((res = 0) => {
        // res 默认值为0
        this.$store.dispatch('saveCartCount', res)
      })
    }
  }
}
</script>
<style lang="scss">
@import '@/assets/scss/reset.scss';
@import '@/assets/scss/config.scss';
@import '@/assets/scss/button.scss';
@import '@/assets/scss/base.scss';
@import '@/assets/scss/mixin.scss';
</style>
