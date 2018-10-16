<template>
  <div>
    <div class="centered">Skip to Section</div>
    <div class="section-links">
      <button v-scroll-to="'#dean'">Dean Palmer</button>
      <button v-scroll-to="'#guests'">Guest</button>
      <button v-scroll-to="'#videos'">Videos</button>
    </div>
    <h3 id="dean">Photos by Dean Palmer</h3>
    <vue-picture-swipe v-if="deanPhotos.length" :items="deanPhotos" :options="options"></vue-picture-swipe>
    <h3 id="guests">Photos Submitted by Guests</h3>
    <vue-picture-swipe v-if="guestPhotos.length" :items="guestPhotos" :options="options"></vue-picture-swipe>
  </div>
</template>

<script>
import VuePictureSwipe from 'vue-picture-swipe';
import axios from 'axios';

export default {
  name: "App",
  components: {
    VuePictureSwipe
  },
  data() {
    return {
      options: {
        shareEl: false,
        zoomEl: false,
      },
      photosMetadata: [],
      deanPhotos: [],
      guestPhotos: [],
      windowHeight: 0
    }
  },
  created() {
    this.windowHeight = window.innerHeight;
    
    axios.get('/photo_metadata.json').then(({ data : { resources } }) => {
      this.photosMetadata = resources;

      this.photosMetadata.forEach(photo => {
        const landscape = photo.width > photo.height;
        const dean = photo.public_id.includes('Dean');
        const photoObj = {
          src: `https://res.cloudinary.com/tkilgour/image/upload/h_${this.windowHeight * 2}/v1539392846/${photo.public_id}.${photo.format}`,
          thumbnail: `https://res.cloudinary.com/tkilgour/image/upload/c_thumb,${landscape ? "h_250" : "w_250"}/v1539392846/${photo.public_id}.${photo.format}`,
          w: photo.width,
          h: photo.height
        }

        dean ? this.deanPhotos.push(photoObj) : this.guestPhotos.push(photoObj);
      })
    })
  }
}
</script>

<style>
img[src*="w_250"]:not(.pswp__img) {
  max-width: 125px;
  margin-top: -10%;
}

img[src*="h_250"]:not(.pswp__img) {
  max-height: 125px;
  margin-left: -10%;
}

figure[itemprop="associatedMedia"] {
  max-width: 125px;
  max-height: 125px;
  overflow: hidden;
}

@media (max-width: 640px) {
  img[src*="w_250"]:not(.pswp__img) {
    max-width: 109px;
  }

  img[src*="h_250"]:not(.pswp__img) {
    max-height: 109px;
  }

  figure[itemprop="associatedMedia"] {
    margin: 2px;
    max-width: 109px;
    max-height: 109px;
  }
}

@media (max-width: 374px) {
  img[src*="w_250"]:not(.pswp__img) {
    max-width: 90px;
  }

  img[src*="h_250"]:not(.pswp__img) {
    max-height: 90px;
  }

  figure[itemprop="associatedMedia"] {
    max-width: 90px;
    max-height: 90px;
  }
}
</style>
