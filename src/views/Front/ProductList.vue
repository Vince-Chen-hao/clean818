<template>
  <div>
    <div class="productlist-banner ">
      <div
        class="dark-overlay d-flex justify-content-center align-items-center"
      >
        <h1 class="banner-title text-center text-light">購 物 市 集</h1>
      </div>
    </div>

    <section id="NavSearch">
      <div class="container mt-5">
        <!-- breadcrumb -->
        <div class="row justify-content-between">
          <div class="col-md-5">
            <nav aria-label="breadcrumb">
              <ol class="breadcrumb" style="background:none;">
                <li class="breadcrumb-item">
                  <router-link class="text-dark" to="/">首頁</router-link>
                </li>
                <li class="breadcrumb-item">
                  <router-link class="text-dark" to="/product_list"
                    >購物市集</router-link
                  >
                </li>
                <li class="breadcrumb-item" v-if="currentCat !== ''">
                  <span class="text-primary">{{ currentCat }}</span>
                </li>
              </ol>
            </nav>
          </div>

          <!-- search -->
          <div class="col-md-3">
            <div class="input-group mb-3">
              <input
                type="text"
                class="form-control"
                v-model="filterData"
                placeholder="搜尋商品"
                aria-label
                aria-describedby="button2"
                @keyup.enter="filterTitle"
              />
              <div class="input-group-append">
                <button
                  class="input-group-text bg-light"
                  type="button"
                  id="button2"
                  @click="filterTitle"
                >
                  <i class="fas fa-search"></i>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Category -->
    <section id="CategoryFilter">
      <div class="container">
        <div class="row">
          <div class="col-lg-2">
            <div class="sticky-top">
              <div class="list-group mt-4">
                <a
                  href="#"
                  class="list-group-item list-group-item-action list-group-item-primary"
                  @click.prevent="filterPro('全部商品')"
                >
                  <i class="fas fa-align-justify"> </i> 全部商品
                </a>
                <a
                  href="#"
                  class="list-group-item list-group-item-action"
                  @click.prevent="filterPro('碗盤器皿')"
                >
                  <i class="fas fa-mortar-pestle"></i> 碗盤器皿
                </a>
                <a
                  href="#"
                  class="list-group-item list-group-item-action"
                  @click.prevent="filterPro('杯與壺')"
                >
                  <i class="fas fa-mug-hot"></i> 杯與壺
                </a>
                <a
                  href="#"
                  class="list-group-item list-group-item-action"
                  @click.prevent="filterPro('刀叉匙筷')"
                >
                  <i class="fas fa-utensil-spoon"></i> 刀叉匙筷
                </a>
                <a
                  href="#"
                  class="list-group-item list-group-item-action"
                  @click.prevent="filterPro('料理廚具')"
                >
                  <i class="fas fa-home"> </i> 料理廚具
                </a>
              </div>
            </div>
          </div>

          <!-- Productlist -->
          <div class="col-lg-10 text-dark">
            <div class="row my-4">
              <div
                class="col-md-4 mb-4"
                v-for="(item, index) in filterProducts"
                :key="index"
              >
                <div class="card-effect card" v-if="finded">
                  <div class="container-img">
                    <div class="sale-tag" v-if="item.origin_price !== ''">特價</div>
                    <div
                      class="l-point productlist-img"
                      :style="{ backgroundImage: `url(${item.imageUrl})` }"
                      @click="goDetail(item.id)"
                    ></div>
                  </div>
                  <div class="card-body l-point" @click="goDetail(item.id)">
                    <span class="badge category-tag mb-2">{{
                      item.category
                    }}</span>
                    <h5 class="card-title">
                      <h5 class="text-dark">{{ item.title }}</h5>
                    </h5>
                    <p class="card-text">{{ item.content }}</p>
                    <div
                      class="d-flex align-items-baseline"
                    >
                      <del v-if="item.origin_price !== ''" class="origin-price"
                        >NT{{ item.origin_price | currency }}</del
                      >
                      <div class="now-price ml-auto">NT{{ item.price | currency }}</div>
                    </div>
                  </div>
                  <div class="card-footer">
                    <button
                      type="button"
                      class="btn btn-bg btn-block ml-auto text-dark"
                      @click="addtoCart(item.id)"
                    >
                      <i class="fas fa-shopping-bag"></i><span> 立即選購</span>
                    </button>
                  </div>
                </div>
              </div>
            <div class="col-md-12 mb-4" v-if="!finded">
              <div style="height: 200px" class="d-flex justify-content-center align-items-center">
              <h5> Oops ! 沒有符合的商品名稱 </h5>

              </div>
            </div>

            </div>
            <div
              class="row justify-content-center"
              v-if="currentCat === '全部商品' && finded"
            >
              <Pagination
                :pageProps="pagination"
                @pagenum_emit="getProducts"
              ></Pagination>
            </div>
          </div>
        </div>
      </div>
    </section>
    <Shoppingcart />
  </div>
</template>

<script>
import Pagination from '../../components/Share/Pagination.vue';
import Shoppingcart from '../../components/Front/Shoppingcart.vue';

export default {
  components: {
    Pagination,
    Shoppingcart,
  },
  data() {
    return {
      currentCat: '全部商品',
      Allproducts: [],
      filterData: '',
      filterProducts: [],
      pagination: {},
      finded: true,
    };
  },

  methods: {
    // 取得分頁後的產品
    getProducts(page = 1) {
      const vm = this;
      const api = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/products?page=${page}`;
      vm.$store.dispatch('updateLoading', true);
      vm.$http.get(api).then((response) => {
        vm.products = response.data.products;
        vm.filterProducts = response.data.products;
        vm.pagination = response.data.pagination;
        vm.$store.dispatch('updateLoading', false);
      });
    },

    addtoCart(id, qty = 1) {
      this.$store.dispatch('addtoCart', { id, qty });
    },

    // 取得全部產品資訊
    getAllproducts() {
      const vm = this;
      const api = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/products/all`;
      vm.$http.get(api).then((response) => {
        vm.Allproducts = response.data.products;
      });
    },

    // 取得分類的產品
    filterPro(name) {
      const vm = this;
      vm.currentCat = name;
      vm.$store.dispatch('updateLoading', true);
      let newArray = [];
      if (name === '全部商品') {
        vm.finded = true;
        vm.getProducts();
      } else {
        newArray = vm.Allproducts.filter((product) => product.category === name);
        vm.filterProducts = newArray;
        vm.finded = true;
      }
      vm.$store.dispatch('updateLoading', false);
    },

    // 關鍵字查詢產品
    filterTitle() {
      const vm = this;
      vm.filterProducts = vm.Allproducts.filter((item) => item.title.indexOf(vm.filterData) > -1);
      if (vm.filterProducts.length > 0) {
        vm.finded = true;
        vm.currentCat = '';
      } else {
        vm.finded = false;
      }
      vm.filterData = '';
    },
    goDetail(id) {
      this.$router.push(`/product_detail/${id}`);
    },
  },

  created() {
    this.getProducts();
    this.getAllproducts();
  },
};
</script>

<style lang="scss" scoped>
.btn-outline-success:hover {
  color: white;
}

.list-group-item-action:focus {
  color: #6c747c;
  background-color: rgba(243, 222, 126, 0.8);
}

.l-point {
  cursor: pointer;
}

.sticky-top {
  z-index: 0;
}

.productlist-banner {
  min-height: 27vh;
  position: relative;
  background-size: cover;
  background-position: center center;
  background-repeat: no-repeat;
  background-image: url(../../assets/images/cover04.png);

  .banner-title {
    letter-spacing: 10px;
  }
  .dark-overlay {
    min-height: 27vh;
    width: 100%;
    background-color: rgba(121, 90, 32, 0.267);
  }
}

#NavSearch {
  .breadcrumb-item {
    font-size: 16px;
  }
  .breadcrumb {
    padding: 0.75rem 0rem;
  }
}

#CategoryFilter {
  .list-group-item {
    height: 50px;
    font-size: 15px;
    transition: all 0.4s;
    text-align: left;
  }
  .list-group-item:hover {
    cursor: pointer;
    background: rgba(245, 217, 95, 0.8);
  }
  .card-effect {
    transition: all 0.5s;
    &:hover {
      box-shadow: 1px 1px 10px 1px rgb(156, 156, 151);
      filter: brightness(1.1);
    }
  }

  .card-body {
    position:relative;
  }

  .sale-tag {
    position: absolute;
    top:5px;
    right:5px;
    padding:7px 5px;
    background-color: rgba(247, 181, 82, 0.803);
    border-radius: 50% 50% 50% 50%;
    color:white;
  }

  .origin-price {
    left: 0;
    bottom: 0;
    color: #b3b3b3;
  }

  .now-price {
    font-size: 18px;
    color: #c1272d;
  }

  .productlist-img {
    background-size: cover;
    background-position: center center;
    height: 200px;
    transition: all 0.8s;

    &:hover {
      height: 200px;
      transform: scale(1.2);
    }
  }
  .container-img {
    height: 200px;
    overflow: hidden;
  }
  .category-tag {
    background-color: rgb(253, 237, 227);
    color: #534741;
    font-size: 14px;
    border-radius: 5px 0 5px 0;
  }

  .card-footer {
    .btn-bg {
      background-color: #f1be77;
    }
  }
}
</style>
