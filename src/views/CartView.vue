<template>
  <h2>購物車</h2>
  <table class="table align-middle">
    <thead>
      <tr>
        <th>圖片</th>
        <th>商品名稱</th>
        <th>價格</th>
        <th></th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="product in products" :key="product.id">
        <td style="width: 200px">
          <div
            :style="{
              backgroundImage: `url(${product.imageUrl})`,
              height: `100px`,
              backgroundSize: `cover`,
              backgroundPosition: `center`,
            }"
          ></div>
        </td>
        <td>
          {{ product.title }}
        </td>
        <td>
          <div v-if="product.price === product.origin_price" class="h5">
            {{ product.price }} 元
          </div>
          <div v-else>
            <del class="h6">原價 {{ product.origin_price }} 元</del>
            <div class="h5">現在只要 {{ product.price }} 元</div>
          </div>
        </td>
        <td>
          <div class="btn-group btn-group-sm">
            <button
              type="button"
              class="btn btn-outline-secondary"
              @click="openProductModal(product.id)"
              :disabled="isLoadingItem === product.id"
            >
              查看更多
            </button>
            <button
              type="button"
              class="btn btn-outline-danger"
              @click="addToCart(product.id)"
              :disabled="isLoadingItem === product.id"
            >
              <span
                v-show="isLoadingItem === product.id"
                class="spinner-border spinner-border-sm"
              ></span>
              加到購物車
            </button>
          </div>
        </td>
      </tr>
    </tbody>
  </table>
  <!-- pagination 分頁元件 -->
  <!-- 用區域註冊匯入的元件名稱命名，把元件標籤加到畫面上 -->
  <!-- props 內層 pages 外層 pagination / props口訣前內後外，用 v-bind 傳遞資料 -->
  <!-- emit 內層 get-product 外層 getData / emit口訣前內後外，用 v-on 觸發事件 -->
  <pagination :pages="pagination" @get-products="getProducts"> </pagination>
  <!-- 購物車列表 -->
  <div class="text-end">
    <button
      class="btn btn-outline-danger"
      type="button"
      @click="deleteAllCarts"
      :disabled="cartData.carts.length === 0"
    >
      清空購物車
    </button>
  </div>
  <table class="table align-middle">
    <thead>
      <tr>
        <th></th>
        <th>品名</th>
        <th style="width: 150px">數量/單位</th>
        <th>單價</th>
      </tr>
    </thead>
    <tbody>
      <!-- 判斷購物車資料有沒有存在 -->
      <template v-if="cartData.carts">
        <tr v-for="item in cartData.carts" :key="item.id">
          <td>
            <button
              type="button"
              class="btn btn-outline-danger btn-sm"
              @click="removeCartItem(item.id)"
            >
              <i class="bi bi-trash3-fill"></i>
            </button>
          </td>
          <td>
            {{ item.product.title }}
            <div class="text-success">已套用優惠券</div>
          </td>
          <td>
            <div class="input-group input-group-sm">
              <div class="input-group mb-3">
                <select
                  id=""
                  class="form-select"
                  v-model="item.qty"
                  @change="updateCartItem(item)"
                  :disabled="isLoadingItem === item.id"
                >
                  <option
                    :value="num"
                    v-for="num in 20"
                    :key="`${num}${item.id}`"
                  >
                    {{ num }}
                  </option>
                </select>
                <span class="input-group-text" id="basic-addon2">{{
                  item.product.unit
                }}</span>
              </div>
            </div>
          </td>
          <td class="text-end">
            <small class="text-success">折扣價：</small>
            {{ item.product.price }}
          </td>
        </tr>
      </template>
    </tbody>
    <tfoot>
      <tr>
        <td colspan="3" class="text-end">總計</td>
        <td class="text-end">{{ cartData.total }}</td>
      </tr>
      <tr>
        <td colspan="3" class="text-end text-success">折扣價</td>
        <td class="text-end text-success">{{ cartData.final_total }}</td>
      </tr>
    </tfoot>
  </table>
</template>

<script>
export default {
  data() {
    return {
      products: [],
      cartData: {},
      isLoadingItem: '',
    };
  },
  methods: {
    getProducts(page = 1) {
      this.$http.get(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/products/?page = ${page}`).then((res) => {
        console.log(res);
        this.products = res.data.products;
        // console.log(this.products);
      });
    },
    addToCart(id, qty = 1) {
      const data = {
        product_id: id,
        qty,
      };
      this.isLoadingItem = id;
      this.$http.post(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/cart`, { data })
        .then((res) => {
          console.log(res);
          this.isLoadingItem = '';
          this.getCart();
        });
    },
  },
  mounted() {
    this.getProducts();
  },
};
</script>
