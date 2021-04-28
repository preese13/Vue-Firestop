<template>
  <div class="product">
    <!--Yeah, Navbar should be its own component.  added this at the last minute to navigate away from 404 page and just 
    copy and pasted it for consistencies sake -->
    <b-navbar fixed="top" toggleable="lg" type="dark" variant="info">
      <b-navbar-brand><router-link to="/">Home</router-link></b-navbar-brand>
      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>
      <b-collapse id="nav-collapse" is-nav> </b-collapse>
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
          <h5>a table with ordering info!!!!!!!</h5>
          <b-table striped hover :items="orderInfoObject"></b-table>
          <a href="#" class="action-button shadow animate green"
            >BUY MY PRODUCT!</a
          >
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
  props: { id: String },
  data() {
    return {
      product: null,
      slide: 0,
      sliding: null,
      orderInfoObject: [
        { Size: "Small", Price: "$10.99", Shipping: "$5.99" },
        { Size: "Medium", Price: "$11.99", Shipping: "$6.99" },
        { Size: "Large", Price: "$12.99", Shipping: "No" },
        { Size: "Double Large", Price: "$999.99", Shipping: "FREE" },
      ],
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
      .catch(() => this.redirect());
  },
  methods: {
    onSlideEnd() {
      this.sliding = false;
    },
    onSlideStart() {
      this.sliding = true;
    },
    redirect() {
      this.$router.push({
        name: "NotFound",
      });
    },
  },
};
</script>

<style>
.carousel-container {
  width: 100%;
  max-width: 800px;
  padding: 10px;
  width: 65%;
}

.carousel-header {
  display: none;
  text-align: center;
  font-size: 20px;
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

.order-info-container {
  width: 100%;
  margin: 10px;
  justify-content: center;
  display: flex;
  flex-direction: column;
}

.padding-class {
  padding: 10px;
}

.padding-top {
  padding-top: 10px;
}

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

.product-description {
  font-size: 12px;
  border-bottom: 1px solid #eee;
}

.spec-item {
  font-size: 15px;
}

.animate {
  transition: all 0.1s;
  -webkit-transition: all 0.1s;
}

.action-button {
  position: relative;
  padding: 10px 40px;
  margin: 0px 10px 10px 0px;
  float: left;
  border-radius: 10px;
  font-family: "Poppins", sans-serif;
  font-size: 25px;
  color: #fff;
  text-decoration: none;
}

.action-button :hover {
  color: white !important;
}

.green {
  background-color: #82bf56;
  border-bottom: 5px solid #669644;
  text-shadow: 0px -2px #669644;
}

.action-button:active {
  transform: translate(0px, 5px);
  -webkit-transform: translate(0px, 5px);
  border-bottom: 1px solid;
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
