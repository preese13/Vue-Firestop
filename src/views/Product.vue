<template>
  <div class="product">
    <div class="product-page-container" v-if="product">
      <h3 class="padding-class">
        {{product.name}}
      </h3>
    <b-carousel
        id="carousel-1"
        v-model="slide"
        :interval="4000"
        controls
        indicators
        background="#ababab"
        style="text-shadow: 1px 1px 2px #333;"
        @sliding-start="onSlideStart"
        @sliding-end="onSlideEnd">
          <b-carousel-slide v-for="image in productImages" :key="image.id" v-bind:img-src="image.url" ></b-carousel-slide>
    </b-carousel>
    <h5 class="padding-top">
      Details
    </h5>
    <p class="padding-class">
      {{ product.description }}
    </p>
    <h5>
      Specs
    </h5>
    <b-list-group>
      <b-list-group-item class="padding-class" v-for="bullet in bulletPoints" :key="bullet.id"> {{ bullet.bulletPoint }} </b-list-group-item>
    </b-list-group>
    </div>
    <div v-else>
      <b-spinner label="Loading..."></b-spinner>
    </div>
  </div>
</template>

<script>
  export default {
    props: ['id'],
    data() {
      return {
        product: null,
        slide: 0,
        sliding: null
      }
    },
    computed: {
      productImages: function() {
        var productImages = []
        this.product.images.forEach(function (item) {
          //filtering out images based on size to save a modicum of time as the images coming in are different sizes and aspect ratios... which is odd
          //results in a console warning 'instance uuid...' Mostly doing this as I don't feel this specific issue is the focus of the exercise 
          if(item.urls.md && (item.width === 600 && item.height === 600))
            productImages.push({ 'url': item.urls.sm, 'id': item.id })
        });
        return productImages
      },
      bulletPoints: function() {
        var bulletPoints = []
        var idGenerator = 0
        this.product.bullet_points.forEach(function (item) {
          bulletPoints.push({'bulletPoint': item, 'id': idGenerator})
          idGenerator++;
        });
        return bulletPoints
      }
    },
    mounted() {
      console.log(this.id)
          fetch('https://api.stifirestop.com/products/' + this.id + '?load%5b%5d=images&load%5b%5d=ordering_items')
      .then(res => res.json())
      .then(data => this.product = data.data)
      .catch(err => console.log(err.message))
    },
        methods: {
      onSlideStart(slide) {
        this.sliding = true
      },
      onSlideEnd(slide) {
        this.sliding = false
      }
    }
  }
</script>

<style>
.product {
  margin-left: auto;
  margin-right: auto;
  display: flex;
  justify-content: center;
}

.product-page-container {
  max-width: 500px;
}

.padding-class{
  padding: 10px;
}

.padding-top {
  padding-top: 10px;
}
</style>
