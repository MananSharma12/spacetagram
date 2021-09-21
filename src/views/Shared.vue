<template>
    <v-main>
      <v-card class="mx-auto my-3" :loading="loading" max-width="50vw">
          <v-skeleton-loader v-show="showSkeleton" type="card-avatar, article" ></v-skeleton-loader>
          <template slot="progress">
            <v-progress-linear color="#0b3d91" height="5" indeterminate></v-progress-linear>
          </template>
      </v-card>

      <div v-for="data in cardData" :key="data.date">
        <Card v-bind="data" :loading="loading"/>
      </div>
    </v-main>
</template>

<script>
import Card from '../components/Card.vue'

export default {
  name: 'App',
  components: {
    Card
  },
  data() {
    return {
      cardData: [],
      loading: true,
      showSkeleton: true,
    }
  },
  created() {
    this.loading = true
    this.showSkeleton = true
    fetch(`https://api.nasa.gov/planetary/apod?api_key=wHwbAF1WvariW0nxBgaraqBoXsZ1E52dD8a43Rtj&date=${this.$route.query.date}`)
      .then(async response => {
        const data = await response.json()
        
        if(!response.ok) {
            const error = (data && data.message) || response.statusText;
            return Promise.reject(error);
        } else {
          this.cardData.push(data)
          this.loading = false
          this.showSkeleton = false
        }
      }) 
   },
};
</script>

<style scoped>
</style>