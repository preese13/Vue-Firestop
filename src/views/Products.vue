<template>
  <div class="products">
    <div v-if="products">
      <div class="products-container">
      <div class="products-item" v-for="product in products" :key="product.id">
        <router-link :to="{ name: 'Product', params: { id: product.id }}">
          <img v-bind:src="product.images[0].urls.sm" />
          <h3 class="padding-side-class products-title">{{ product.name }}</h3>
          <p class="padding-class products-p"> {{ product.description.substr(0, 100) }}...</p>
      </router-link>
      </div>
      </div>
    </div>
    <div v-else>
       <b-spinner label="Loading..."></b-spinner>
       </div>
  </div>
</template>

<script>
export default {
  name: 'Products',
  data() {
    return {
      products: null,
      page: 1,
      limit: 25
    }
  },
  mounted() {
    fetch('https://api.stifirestop.com/products?page=' + '1' + '&limit=' + 25 + '&load%5b%5d=images')
      .then(res => res.json())
      .then(data => this.products = data.data)
      .catch(err => console.log(err.message))
  },
}
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
  margin-bottom: 0px;
  font-size: 18px;
}

.products-p {
  color: black;
  font-size: 12px;
  text-decoration: none !important;
}

.products-p :hover {
  text-decoration: none !important;
}

.products-item {
max-width: 400px;
margin: 50px;
}

@media only screen and (max-width: 1515px) {
  .products-item {
    margin:none;
    max-width: 300px;
  }
}
</style>
