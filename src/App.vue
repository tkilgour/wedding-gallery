<template>
  <div>
    <h3>Photos by Dean Palmer</h3>
    <vue-picture-swipe v-if="deanPhotos.length" :items="deanPhotos" :options="options"></vue-picture-swipe>
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
      windowHeight: 0
    }
  },
  created() {
    this.windowHeight = window.innerHeight;
    
    axios.get('/photo_metadata.json').then(({ data : { resources } }) => {
      this.photosMetadata = resources;

      this.photosMetadata.forEach(photo => {
        this.deanPhotos.push({
          src: `https://res.cloudinary.com/tkilgour/image/upload/h_${this.windowHeight * 2}/v1539392846/${photo.public_id}.${photo.format}`,
          thumbnail: `https://res.cloudinary.com/tkilgour/image/upload/c_thumb,h_250/v1539392846/${photo.public_id}.${photo.format}`,
          w: photo.width,
          h: photo.height
        });
      })
    })
  }
}
</script>

<style>
img[itemprop="thumbnail"] {
  max-height: 125px;
}

@media (max-width: 640px) {
  img[itemprop="thumbnail"] {
    max-height: 100px;
  }

  figure[itemprop="associatedMedia"] {
    margin: 0;
  }
}

@media (max-width: 360px) {
  img[itemprop="thumbnail"] {
    max-height: 87px;
  }
}
</style>
