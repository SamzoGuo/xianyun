<template>
  <section class="contianer">
    <el-row type="flex" justify="space-between">
      <!-- 顶部过滤列表 -->
      <div class="flights-content">
        <!-- 过滤条件 -->
        <FlightsFilters :data="cacheFlightsData" @setDataList="setDataList" />

        <!-- 航班头部布局 -->
        <FlightsListHead />

        <!-- 航班信息 -->
        <FlightsItem v-for="(item,index) in dataList" :key="index" :item="item" />
        <div
          style="padding:30px;text-align:center"
          v-if="flightsData.flights.length===0&&!loading"
        >该航班暂无数据</div>
        <el-pagination
          v-if="flightsData.flights.length"
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="pageIndex"
          :page-sizes="[5, 10, 15]"
          :page-size="pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        ></el-pagination>
      </div>
      <!-- 侧边栏 -->
      <div class="aside">
        <!-- 侧边栏组件 -->
        <FlightsAside />
      </div>
    </el-row>
  </section>
</template>

<script>
import FlightsListHead from "@/components/air/flightsListHead";
import FlightsItem from "@/components/air/flightsItem";
import FlightsFilters from "@/components/air/flightsFilters";
import FlightsAside from "@/components/air/flightsAside";

export default {
  data() {
    return {
      // 请求机票列表返回的总数据，包含了flights,info, options,total
      flightsData: {
        //初始值
        flights: [],
        info: {},
        options: {}
      },
      // 声明多一分总数据，`该总数据一旦赋值之后不会再被修改`，也就是第一次赋值完后的值等于flightsData
      cacheFlightsData: {
        // 初始值
        flights: [],
        info: {},
        options: {}
      },
      //当前页数
      pageIndex: 1,
      //当前条数
      pageSize: 5,
      //判断是否加载完成
      loading: true,
      // 分页条数
      total: 0
    };
  },
  components: {
    FlightsListHead,
    FlightsItem,
    FlightsFilters,
    FlightsAside
  },
  mounted() {
    //请求机票列表
   this.getList();
  },
  methods: {
    // 分页条数切换时候触发, val是当前的条数
    handleSizeChange(val) {
      this.pageSize = val;
    },
    // 页数切换时候触发, val是当前的页数
    handleCurrentChange(val) {
      this.pageIndex = val;
    },
    //给过滤组件修改flightsData中的flights
    setDataList(arr) {
      this.flightsData.flights = arr;
      //修改分页的初始值
      this.total = arr.length;
      this.pageIndex = 1;
    },
    //请求机票列表
    getList() {
      const data = this.$route.query;
      this.$axios({
        url: "/airs",
        // params是axios的get的参数
        params: data
      }).then(res => {
        // 保存到机票的总数据
        this.flightsData = res.data;
        // 赋值多一分给缓存的对象,一旦赋值之后不能再被修改
        this.cacheFlightsData = { ...res.data };
        // 请求完毕
        this.loading = false;
        // 分页总数
        this.total = this.flightsData.total;
      });
    }
  },
  computed: {
    dataList() {
      // 从flights总列表数据中切割出来新数组列表渲染页面
      const arr = this.flightsData.flights.slice(
        (this.pageIndex - 1) * this.pageSize,
        this.pageIndex * this.pageSize
      );
      return arr;
    }
  },
  watch: {
    $route() {
      //请求机票列表
      this.getList();
    }
  }
};
</script>

<style scoped lang="less">
.contianer {
  width: 1000px;
  margin: 20px auto;
}
.flights-content {
  width: 745px;
  font-size: 14px;
}
.aside {
  width: 240px;
}
</style> 