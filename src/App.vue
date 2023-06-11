<template>
  <div class="app-container">
    <!-- header头部区域 -->
    <Header title="购物车案例"></Header>
    <h1>App 根组件</h1>
    <!-- 循环渲染每一个商品的信息 -->
    <Goods v-for="item in list" :key="item.id" :title="item.goods_name" :pic="item.goods_img" :state="item.goods_state" :id="item.id" :count="item.goods_count" @state-change="getNewState">
      <Counter :num="item.goods_count" @num-change="getNewNum(item. $event)"></Counter>
    </Goods>
    <!-- Footer区域 -->
    <!-- 编码规范：先指令，再绑定，再事件 -->
    <Footer :isfull="fullState" :amount="amt" :all="total" @full-change="getFullState"></Footer>
  </div>
</template>

<script>
//导入 axios 请求库
import axios from 'axios';

import Header from "./components/Header/Header.vue";
import Goods from "./components/Goods/Goods.vue";
import Footer from "./components/Footer/Footer.vue"

import bus from './components/eventBus.js'
import Counter from './components/Counter/Counter.vue';

export default {
  data() {
    return {
      // 用来存储购物车的列表数据，默认为空数组
      list:[]

    }
  },
  //计算属性
  computed: {
    //动态计算出全选的状态是true还是false
    fullState() {
      return this.list.every(item => item.goods_state)
    },
    //已勾选商品的总价格
    amt() {
      // 1.先 filter过滤
      // 2. 再reduce累加
      this.list.filter(item => item.goods_state).reduce((total, item) => {
        total += item.goods_price * item.goods_count
      }, 0)
    },
      // 已勾选商品的总数量
    total() {
      return this.list.filter(item => item.goods_state).reduce((t, item) => (t += item.goods_count), 0)
    }
  },

  created() {
    //调用请求数据的方法
    this.initCartList()
    bus.$on('share', val => {
      this.list.some(item => {
        if(item.id === val.id) {
          item.goods_count = val.value
          return true
        }
      })
    })
  },
  methods: {
    //封装请求列表数据的方法
    async initCartList() {
      // 调用 axios 的get方法，请求列表数据
      const {data: res} = await axios.get('https://www.escook.cn/api/cart')
      //只要请求回来的数据，在页面渲染期间要用到，则必须转存到data中
      if(res.status == 200) {
        this.list = res.list
      }
    },
    //接收子组件传递过来的数据
    //e的格式为{ id, value}
    getNewState(e) {
      console.log('父组件接收到了数据');
      if(item.id === e.id) {
        item.goods_state = e.value
      }
    },
    //接收 Footer 子组件传递过来的全选按钮的状态
    getFullState(val) {
      console.log('在App中拿到了全选的状态');
      this.list.forEach(item => (item.goods_state = val))
    },
    // 获取 Counter 组件发过来的最新的数量值
    getNewNum(item, e) {
      item.goods_count = e
    }
  },
  components: {
    Header,
    Goods,
    Footer,
    Counter
  }
  
}
</script>

<style lang="less" scoped>
.app_container {
  padding-top: 45px;
  padding-bottom: 50px;
}
</style>
