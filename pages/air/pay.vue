<template>
  <div class="container">
    <div class="main">
      <div class="pay-title">
        支付总金额
        <span class="pay-price">￥ {{order.price}}</span>
      </div>
      <div class="pay-main">
        <h4>微信支付</h4>
        <el-row type="flex" justify="space-between" align="middle" class="pay-qrcode">
          <div class="qrcode">
            <!-- 二维码 -->
            <canvas id="qrcode-stage"></canvas>
            <p>请使用微信扫一扫</p>
            <p>扫描二维码支付</p>
          </div>
          <div class="pay-example">
            <img src="http://157.122.54.189:9093/images/wx-sweep2.jpg" alt />
          </div>
        </el-row>
      </div>
    </div>
  </div>
</template>

<script>
//引入生成二维码的包
import QRcode from "qrcode";
export default {
  data() {
    return {
      order: {}
    };
  },
  mounted() {
    //通过订单id获取订单详情
    const { id } = this.$route.query;
    setTimeout(() => {
      this.$axios({
        url: `/airorders/` + id,
        headers: {
          Authorization: `Bearer ${this.$store.state.user.userInfo.token}`
        }
      }).then(res => {
        this.order = res.data;
        //生成二维码
        //查找存放二维码的元素
        const qrcode = document.querySelector("#qrcode-stage");
        //二维码生成的方法 第一个参数是元素  第二个是生成二维码链接
        QRcode.toCanvas(qrcode, this.order.payInfo.code_url);
        //轮询状态
        const timer=setInterval(() => {
          this.$axios({
            url: "/airorders/checkpay",
            method: "POST",
            headers: {
              Authorization: `Bearer ${this.$store.state.user.userInfo.token}`
            },
            data: {
              id: this.$route.query.id,
              nonce_str: this.order.price,
              out_trade_no: this.order.orderNo
            }
          }).then(res => {
           const {statusTxt}=res.data
           if(statusTxt=='支付完成'){
             this.$message.success(statusTxt)
             clearInterval(timer)
           }
          });
        },3000);
      });
    }, 10);
  }
};
</script>

<style scoped lang="less">
.container {
  background: #f5f5f5;
  padding: 30px 0;

  .main {
    width: 1000px;
    margin: 0 auto;

    .pay-title {
      text-align: right;
      span {
        font-size: 28px;
        color: orangered;
      }
    }

    .pay-main {
      background: #fff;
      margin-top: 10px;
      border-top: 5px orange solid;
      padding: 30px;

      h4 {
        font-size: 28px;
        font-weight: normal;
        margin-bottom: 10px;
      }

      .pay-qrcode {
        padding: 0 80px;
      }

      .qrcode {
        border: 1px #ddd solid;
        padding: 15px;
        height: fit-content;

        #qrcode-stage {
          width: 200px;
          height: 200px;
          margin-bottom: 10px;
        }

        p {
          line-height: 2;
          text-align: center;
        }
      }
    }
  }
}
</style>