<template>
  <div class="product">
      <b-navbar fixed="top" toggleable="lg" type="dark" variant="info">
    <b-navbar-brand ><router-link to="/">Products</router-link></b-navbar-brand>
    <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>
    <b-collapse id="nav-collapse" is-nav>
      <b-navbar-nav>
        <b-nav-item href="#">Link</b-nav-item>
        <b-nav-item href="#" disabled>Disabled</b-nav-item>
      </b-navbar-nav>
    </b-collapse>
  </b-navbar>
    <div v-if="product" class="product-page-container">
      <div class="carousel-container">
        <h1 class="padding-class carousel-header">
          {{ product.name }}
        </h1>
        <b-carousel
          id="carousel-1"
          v-model="slide"
          :interval="4000"
          controls
          indicators
          background="#ababab"
          style="text-shadow: 1px 1px 2px #333"
          @sliding-start="onSlideStart"
          @sliding-end="onSlideEnd"
        >
          <b-carousel-slide
            v-for="image in productImages"
            :key="image.id"
            :img-src="image.url"
          ></b-carousel-slide>
        </b-carousel>
        <div class="order-info-container">
          test
        </div>
      </div>
      <div class="info-container">
        <h3 class="padding-class info-header">
          {{ product.name }}
        </h3>
        <h5 class="padding-top">Details</h5>
        <p class="padding-class product-description">
          {{ product.description }}
        </p>
        <h5>Specs</h5>
        <b-list-group>
          <b-list-group-item
            v-for="bullet in bulletPoints"
            :key="bullet.id"
            class="padding-class spec-item"
          >
            {{ bullet.bulletPoint }}
          </b-list-group-item>
        </b-list-group>
      </div>
    </div>
    <div v-else>
      <b-spinner label="Loading..."></b-spinner>
    </div>
  </div>
</template>

<script>
export default {
  props: ["id"],
  data() {
    return {
      product: null,
      slide: 0,
      sliding: null,
    };
  },
  computed: {
    productImages: function () {
      var productImages = [];
      this.product.images.forEach(function (item) {
        //filtering out images based on size to save a modicum of time as the images coming in are different sizes and aspect ratios... which is odd
        //results in a console warning 'instance uuid...' Mostly doing this as I don't feel this specific issue is the focus of the exercise
        if (item.urls.md && item.width === 600 && item.height === 600)
          productImages.push({ url: item.urls.sm, id: item.id });
      });
      return productImages;
    },
    bulletPoints: function () {
      var bulletPoints = [];
      var idGenerator = 0;
      this.product.bullet_points.forEach(function (item) {
        bulletPoints.push({ bulletPoint: item, id: idGenerator });
        idGenerator++;
      });
      return bulletPoints;
    },
  },
  mounted() {
    fetch(
      "https://api.stifirestop.com/products/" +
        this.id +
        "?load%5b%5d=images&load%5b%5d=ordering_items"
    )
      .then((res) => res.json())
      .then((data) => (this.product = data.data))
      .catch((err) => console.log(err.message));
  },
  methods: {
    onSlideStart() {
      this.sliding = true;
    },
    onSlideEnd() {
      this.sliding = false;
    },
  },
};
</script>

<style>
.product {
  margin-left: auto;
  margin-right: auto;
  display: flex;
  justify-content: center;
}

.product-page-container {
  max-width: 1000px;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
}

.orer-info-container {
  width: 100%;
  height: 100px;
  background-color: red;
}

.padding-class {
  padding: 10px;
}

.padding-top {
  padding-top: 10px;
}

.carousel-container {
  width: 100%;
  max-width: 800px;
  padding: 10px;
  width: 65%;
}

.info-container {
  max-width: 700px;
  padding: 10px;
  width: 33%;
}

.info-header {
    text-align: center;
  font-size: 20px;
  border-bottom: 1px solid #eee;
}

.carousel-header {
  display: none;
  text-align: center;
  font-size: 20px;
}

.product-description {
  font-size: 12px;
  border-bottom: 1px solid #eee;
}

.spec-item {
  font-size: 15px;
}

@media only screen and (max-width: 750px) {

  .carousel-header {
    display: block;
  }

  .info-header {
    display: none;
  }

  .product-page-container {
    flex-direction: column;
    width: 100%;
    justify-content: center;
    align-items: center;
  }
  .carousel-container {
    width: 100%;
  }

  .info-container {
    width: 100%;
  }
}
</style>
