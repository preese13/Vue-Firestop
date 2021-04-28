<template>
  <div class="products">
    <div>
      <b-navbar fixed="top" toggleable="lg" type="dark" variant="info">
        <b-navbar-brand><router-link to="/">Home</router-link></b-navbar-brand>
        <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>
        <b-collapse id="nav-collapse" is-nav>
          <b-navbar-nav>
            <b-nav-item @click="toggleInfiniteScroll"
              >Infinite Scroll</b-nav-item
            >
          </b-navbar-nav>
          <!-- Right aligned nav items -->
          <b-navbar-nav class="ml-auto">
            <b-nav-form>
              <input
                v-model="filterString"
                placeholder="Filter"
                type="search"
                @input="filterByName"
              />
            </b-nav-form>
            <!-- API is givine me issues, might get to this one
           <b-nav-form  @submit.prevent="searchByName">
          <input placeholder="Search" type="search" v-model="searchString">
          <button type="submit">
            Submit
          </button>
        </b-nav-form>-->
          </b-navbar-nav>
        </b-collapse>
      </b-navbar>
    </div>
    <div class="options-container-container">
      <div class="options-container">
        <p class="options-title">Items Per Page:</p>
        <select v-model="itemsPerPage" @change="selectItemsPerPage">
          <option disabled value="">Items Per Page</option>
          <option :value="5">5</option>
          <option :value="25">25</option>
          <option :value="50">50</option>
        </select>
      </div>
      <div class="options-container">
        <p class="options-title">Sort By:</p>
        <select v-model="SortOrder" @change="sortBy">
          <option disabled selected value="Sort By">Sort Items</option>
          <option :value="1">Name Ascending</option>
          <option :value="2">Name Descending</option>
          <option :value="3">Popularity Ascending</option>
          <option :value="4">Popularity Descending</option>
        </select>
      </div>
    </div>
    <div v-if="products">
      <div class="products-container">
        <div
          v-for="product in products"
          :key="product.id"
          class="products-item"
        >
          <router-link :to="{ name: 'Product', params: { id: product.id } }">
            <img class="product-img" :src="product.images[0].urls.sm" />
            <h3 class="padding-side-class products-title">
              {{ product.name }}
            </h3>
          </router-link>
          <p class="padding-class products-p">
            {{ product.description.substr(0, 100) }}...
          </p>
        </div>
      </div>
      <div v-if="infiniteScroll">
        <b-spinner v-if="loadingMoreContent" label="Loading..."></b-spinner>
        <button v-else @click="loadMore">Load More</button>
      </div>
      <div v-else>
        <b-spinner v-if="loadingMoreContent" label="Loading..."></b-spinner>
        <div v-else>
          <button id="prev" @click="changePage">Prev Page</button>
          <button id="next" @click="changePage">Next Page</button>
        </div>
      </div>
    </div>
    <div v-else class="full-page-spinner">
      <b-spinner label="Loading..."></b-spinner>
    </div>
  </div>
</template>

<script>
export default {
  name: "Products",
  data() {
    return {
      products: null,
      productsBackup: null,
      page: 1,
      itemsPerPage: 25,
      loadingMoreContent: false,
      filterString: "",
      SortOrder: null,
      searchString: "",
      infiniteScroll: true,
    };
  },
  mounted() {
    this.fetchProducts("mounted");
  },
  methods: {
    checkForEmptyReturnObj() {
      //when getting products where page = page+1, ensure the array isn't empty because we've surpassed the number of pages in db
      if (this.products.length === 0) {
        this.products = this.productsBackup;
      } else {
        this.productsBackup = this.products;
      }
    },
    fetchProducts(origin) {
      //all requests are minute variations on same request, so I've bundled them here
      if (origin === "changePage") {
        // click next/prev page
        fetch(
          "https://api.stifirestop.com/products?page=" +
            this.page +
            "&limit=" +
            this.itemsPerPage +
            "&load%5b%5d=images"
        )
          .then((res) => res.json())
          .then(
            (data) => (
              (this.products = data.data),
              (this.loadingMoreContent = false),
              this.checkForEmptyReturnObj(),
              this.sortBy()
            )
          )
          .catch((err) => alert(err.message));
      }

      if (origin === "itemsPerPage" || origin === "mounted") {
        //change items per page || onMount
        fetch(
          "https://api.stifirestop.com/products?page=" +
            this.page +
            "&limit=" +
            this.itemsPerPage +
            "&load%5b%5d=images"
        )
          .then((res) => res.json())
          .then(
            (data) => (
              (this.products = data.data),
              (this.loadingMoreContent = false),
              (this.productsBackup = this.product),
              this.sortBy()
            )
          )
          .catch((err) => alert(err.message));
      }

      if (origin === "loadMore") {
        // infinite scroll
        fetch(
          "https://api.stifirestop.com/products?page=" +
            this.page +
            "&limit=" +
            this.itemsPerPage +
            "&load%5b%5d=images"
        )
          .then((res) => res.json())
          .then(
            (data) => (
              (this.products = this.products.concat(data.data)),
              (this.productsBackup = this.products),
              (this.loadingMoreContent = false),
              this.sortBy()
            )
          )
          .catch((err) => alert(err.message));
      }
    },
    changePage(e) {
      if (e.target.id === "next") {
        this.page = this.page + 1;
        this.products = null;
        this.fetchProducts("changePage");
      } else if (e.target.id === "prev") {
        if (this.page === 1) {
          return;
        }
        this.page = this.page - 1;
        this.products = null;
        this.fetchProducts("changePage");
        this.productsBackup = this.products;
      }
    },
    //since the API is giving me trouble, I'm doing some javascript filtering
    filterByName(e) {
      if (e.data === null) {
        //when user deletes character in fiter string, restore array to backup and filter again
        this.products = this.productsBackup;
      }
      this.products = this.products.filter(
        (product) =>
          product.name.toLowerCase().indexOf(this.filterString.toLowerCase()) >=
          0
      );
    },

    loadMore() {
      this.page = this.page + 1;
      this.loadingMoreContent = true;
      this.fetchProducts("loadMore");
    },

    selectItemsPerPage() {
      this.products = null;
      this.page = 1;
      this.fetchProducts("itemsPerPage");
    },

    // API is giving me issues, sorting on the client side for funsies
    sortBy() {
      if (this.SortOrder === null) {
        return;
      } else if (this.SortOrder === 1) {
        //name ascending
        this.products.sort((a, b) => (a.name > b.name ? 1 : -1));
      } else if (this.SortOrder === 2) {
        // name descending
        this.products.sort((a, b) => (b.name > a.name ? 1 : -1));
      } else if (this.SortOrder === 3) {
        // popularity ascending
        this.products.sort((a, b) => a.popularity - b.popularity);
      } else if (this.SortOrder === 4) {
        //popularity descending
        this.products.sort((a, b) => b.popularity - a.popularity);
      }
    },
    toggleInfiniteScroll() {
      this.infiniteScroll = !this.infiniteScroll;
    },

    /*  API is giving me issues, may or may not get back to this one 
   searchByName() {
      this.products = null
      this.page = 1
      fetch(
      "https://api.stifirestop.com/products?search=" + this.searchByName + "&load%5b%5d=images"
    )
      .then((res) => res.json())
      .then((data) => (
        this.products = data.data,
        this.productsBackup = this.products,
        this.sortBy()
        ))
      .catch((err) => alert(err.message));
    },*/
  },
};
</script>

<style>
.products-container {
  display: flex;
  flex-direction: row;
  flex-flow: row wrap;
  justify-content: space-around;
}

.padding-class {
  padding-left: 10px;
  padding-right: 10px;
}

.products-title {
  margin: auto;
  margin-bottom: 0px;
  font-size: 18px;
  text-align: center;
  width: 100%;
}

.products-p {
  color: black;
  font-size: 12px;
  text-decoration: none !important;
  height: 100%;
  width: 100%;
}

.products-p :hover {
  text-decoration: none !important;
}

.products-item {
  max-width: 250px;
  margin: 50px;
}

.products {
  max-width: 1200px;
  margin: auto;
}

.product-img {
  max-width: 300px;
  margin: auto;
}

.full-page-spinner {
  display: flex;
  justify-content: center;
  margin: 25%;
}

.options-container {
  display: flex;
  flex-direction: row;
  width: 50%;
  justify-content: center;
  margin-top: 10px;
}
.options-container-container {
  display: flex;
  flex-direction: row;
  width: 100%;
  justify-content: center;
}

.options-title {
  margin: 10px;
}

@media only screen and (max-width: 714px) {
  .options-container {
    width: 100%;
  }
  .options-container-container {
    flex-direction: column;
  }
}
</style>
