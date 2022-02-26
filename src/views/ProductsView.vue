<template>
  <h2>產品列表</h2>
  <div class="container">
    <div class="row row-cols-1 row-cols-sm-2 row-cols-md-3 row-cols-lg-4 g-3">
      <div class="col" v-for="product in products" :key="product.id">
        <div class="card h-100">
          <img :src="product.imageUrl" class="card-img-top" alt="productImage" />
          <div class="card-body">
            <h5 class="card-title">{{ product.title }}</h5>
            <p class="card-text">
              {{ product.description }}
            </p>
            <router-link :to="`/product/${product.id}`" class="btn btn-primary">
              查看更多
            </router-link>
          </div>
        </div>
      </div>
    </div>
  </div>
  <!-- pagination 分頁元件 -->
  <!-- 用區域註冊匯入的元件名稱命名，把元件標籤加到畫面上 -->
  <!-- props 內層 pages 外層 pagination / props口訣前內後外，用 v-bind 傳遞資料 -->
  <!-- emit 內層 get-product 外層 getData / emit口訣前內後外，用 v-on 觸發事件 -->
  <pagination :pages="pagination" @get-products="getProducts"> </pagination>
</template>

<script>
import Pagination from '@/components/Pagination.vue';

export default {
  data() {
    return {
      products: [],
      pagination: {},
    };
  },
  components: {
    Pagination,
  },

  methods: {
    getProducts(page = 1) {
      this.$http.get(`${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/products/?page = ${page}`)
        .then((res) => {
          console.log(res);
          this.products = res.data.products; // 賦值
          // 取出分頁資訊
          this.pagination = res.data.pagination;
        })
        .catch((err) => {
          console.dir(err.response.data.message);
        });
    },
  },
  mounted() {
    this.getProducts();
  },
};
</script>
