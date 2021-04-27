<template>
  <div class="products">
    <div>
  <b-navbar fixed="top" toggleable="lg" type="dark" variant="info">
    <b-navbar-brand ><router-link to="/">Products</router-link></b-navbar-brand>
    <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>
    <b-collapse id="nav-collapse" is-nav>
      <b-navbar-nav>
        <b-nav-item href="#">Link</b-nav-item>
        <b-nav-item href="#" disabled>Disabled</b-nav-item>
      </b-navbar-nav>
      <!-- Right aligned nav items -->
      <b-navbar-nav class="ml-auto">
        <b-nav-form>
          <input placeholder="Search" type="search" v-model="filterString" @input="filterByName">
          <b-button size="sm" class="my-2 my-sm-0" type="submit">Search</b-button>
        </b-nav-form>
      </b-navbar-nav>
    </b-collapse>
  </b-navbar>
</div>
<select v-model="itemsPerPage" @change="selectItemsPerPage">
  <option disabled value="">Items Per Page</option>
  <option v-bind:value="5">5</option>
  <option v-bind:value="25">25</option>
  <option v-bind:value="50">50</option>
</select>
<select v-model="SortOrder" @change="sortBy">
  <option disabled value="">Sort Items</option>
  <option v-bind:value="1">Name Ascending</option>
  <option v-bind:value="2">Name Descending</option>
  <option v-bind:value="3">Popularity Ascending</option>
  <option v-bind:value="4">Popularity Descending</option>
</select>
    <div v-if="products">
      <div class="products-container">
        <div
          v-for="product in products"
          :key="product.id"
          class="products-item"
        >
          <router-link :to="{ name: 'Product', params: { id: product.id } }">
            <img  class="product-img" :src="product.images[0].urls.sm" />
            <h3 class="padding-side-class products-title">
              {{ product.name }}
            </h3>
          </router-link>
            <p class="padding-class products-p">
              {{ product.description.substr(0, 100) }}...
            </p>
        </div>
      </div>
      <b-spinner v-if="loadingMoreContent" label="Loading..."></b-spinner>
      <button @click="loadMore" v-else> Load More</button>
    </div>
    <div class="full-page-spinner" v-else>
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
      filterString: '',
      SortOrder: null,
    };
  },
  methods: {

    //In a prod env where I have more time I'd filter what is rendered in the dom as opposed
    //to filtering the array of products itself.  However, time is of the essence and I'd rather 
    //get this in than skip it.  So I've concocted the 'productsBackup' as a quick way to restore
    // the products array when a user removes a character from the search string
    filterByName(e) {
      console.log(e)
      if(e.data === null) { //when user deletes character in fiter string, restore array to backup and filter again
        this.products = this.productsBackup
      }
      this.products = this.products.filter(product =>
        product.name.toLowerCase().indexOf(this.filterString.toLowerCase()) >= 0);
    },
    sortBy() {
      if(this.SortOrder === 1) { //name ascending
        this.products.sort((a,b)=> (a.name > b.name ? 1 : -1))
      }
      else if(this.SortOrder === 2) { // name descending
        this.products.sort((a,b)=> (b.name > a.name ? 1 : -1))
      }
      else if(this.SortOrder === 3) { // popularity ascending
        this.products.sort((a, b) => a.popularity - b.popularity)
      }
      else if(this.SortOrder === 4) { //popularity descending
        this.products.sort((a, b) => b.popularity - a.popularity)
      }
    },
    //todo:  check if response data[] is empty and hide the 'loadMore' button once all data is retrieved
    //todo: make fetch requests dry.  
    loadMore() {
      this.page = this.page + 1
      this.loadingMoreContent = true
      fetch(
      "https://api.stifirestop.com/products?page=" +
        this.page +
        "&limit=" +
        this.itemsPerPage +
        "&load%5b%5d=images"
    )
      .then((res) => res.json())
      .then((data) => (
        this.products = this.products.concat(data.data),
        this.productsBackup = this.products,
        this.loadingMoreContent = false
        ))
      .catch((err) => console.log(err.message));
    },
    selectItemsPerPage() {
      this.products = null
      this.page = 1
          fetch(
      "https://api.stifirestop.com/products?page=" +
        this.page +
        "&limit=" +
        this.itemsPerPage +
        "&load%5b%5d=images"
    )
      .then((res) => res.json())
      .then((data) => (
        this.products = data.data,
        this.productsBackup = this.products 
      ))
      .catch((err) => console.log(err.message));
    }
  },
  mounted() {
    fetch(
      "https://api.stifirestop.com/products?page=" +
        "1" +
        "&limit=" +
        this.itemsPerPage +
        "&load%5b%5d=images"
    )
      .then((res) => res.json())
      .then((data) => (
        this.products = data.data,
        this.productsBackup = this.products
        ))
      .catch((err) => console.log(err.message));
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
</style>
