<template>
  <v-app>
    <v-app-bar app color="#105bd8" dark>
      <v-img class="shrink mr-2" contain 
        src="https://www.nasa.gov/sites/default/files/thumbnails/image/nasa-logo-web-rgb.png" 
        transition="scale-transition"
        width="100"
      />      
      <v-toolbar-title>Spacetagram</v-toolbar-title>
      <v-spacer></v-spacer>
    </v-app-bar>
    <router-view/>
  </v-app> 
</template>

<script>
// import Card from './components/Card.vue'

export default {
  name: 'App',
  components: {
    // Card
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

    fetch(`https://api.nasa.gov/planetary/apod?api_key=wHwbAF1WvariW0nxBgaraqBoXsZ1E52dD8a43Rtj`)
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
  computed: {
    today() {
      return (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10)
    },
    fetchDate() {
      let dateObj = new Date(this.date)
      return (new Date(dateObj.setDate(dateObj.getDate() - this.cardData.length)).toISOString().split('T')[0])
    }
  },
  methods: {
    fetchNextData() {
      window.onscroll = () => {
        let bottomOfWindow = Math.round(document.documentElement.scrollTop + window.innerHeight) === document.documentElement.offsetHeight;
        if (bottomOfWindow) {
          this.loading = true
          this.showSkeleton = true
          fetch(`https://api.nasa.gov/planetary/apod?api_key=wHwbAF1WvariW0nxBgaraqBoXsZ1E52dD8a43Rtj&count=5`)
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
        }
      };
    }
  },
  mounted() {
    this.fetchNextData()
  } 
};
</script>

<style>
  html {
    font-family: Helvetica, "Source Sans Pro", Merriweather;
  }
</style>