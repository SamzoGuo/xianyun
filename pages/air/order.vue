<template>
  <div class="container">
    <el-row type="flex" justify="space-between">
      <!-- 订单表单 -->
      <OrderForm @getDetail="getDetail" />
      <!-- 侧边栏 -->
      <OrderAside :data="detail" />
    </el-row>
  </div>
</template>

<script>
import OrderForm from "@/components/air/orderForm";
import OrderAside from "@/components/air/OrderAside";
export default {
  data() {
    return {
      //保存
      detail: {
        //默认值
        seat_infos: {}
      }
    };
  },
  components: {
    OrderForm,
    OrderAside
  },
  methods: {
    //机票详情通过事件传递到父组件
    getDetail(detail) {
      this.detail = detail;
    }
  }
};
</script>

<style lang="less" scoped>
.container {
  width: 1000px;
  margin: 20px auto;
}

/*aside*/
.aside {
  width: 350px;
  height: fit-content;
  border: 1px #ddd solid;
}
</style>