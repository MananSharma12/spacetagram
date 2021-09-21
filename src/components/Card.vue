<template>
  <v-card class="mx-auto my-3" :loading="loading" :max-width="maxWidth">
    <v-skeleton-loader v-show="showSkeleton" type="card-avatar, article" ></v-skeleton-loader>
    <template slot="progress">
      <v-progress-linear color="#0b3d91" height="5" indeterminate></v-progress-linear>
    </template>

    <v-img :src="url"></v-img>

    <v-card-title>
        <v-btn large icon @click="like" class="mr-2">
            <v-icon large  :color="heartColor">mdi-heart</v-icon>
        </v-btn>
        {{title}}
        <v-spacer></v-spacer>
        <v-btn icon color="#0b3d91" @click="share" v-if="shareBtn">
            <v-icon>mdi-share</v-icon>
            <v-snackbar v-model="snackbar" timeout="2000">
                Link Copied! 
                <template v-slot:action="{ attrs }">
                    <v-btn color="blue" text v-bind="attrs" @click="snackbar = false">
                        Close
                    </v-btn>
                </template>
            </v-snackbar>
        </v-btn>

    </v-card-title>
    
    <v-card-subtitle class="mt-auto">{{date}}</v-card-subtitle>
    <v-card-text>{{explanation}}</v-card-text>
  </v-card>
</template>

<script>
export default {
    name: "Card",
    data() {
        return {
            showSkeleton: true,
            liked: false,
            snackbar: false,
        }
    },
    props: [ 'date', 'title', 'url', 'explanation', 'loading'],
    created() {
        if(window.localStorage[this.date] == "true") {
            this.liked = true
        }
    },
    mounted() {
        this.$nextTick(() => {
            this.loading = false
            this.showSkeleton = false   
        })
    },
    computed: {
        heartColor() {
            if(this.liked) {
                return "#dd361c"
            } else {
                return "#ccc"
            }
        },
        // eslint-disable-next-line vue/return-in-computed-property
        maxWidth() {
            switch (this.$vuetify.breakpoint.name) {
                case 'xs': return "90vw"
                case 'sm': return "75vw"
                case 'md': return "50vw"
                case 'lg': return "45vw"
                case 'xl': return "40vw"
            }
        },
        link() {
            return `${window.location.href}shared?date=${this.date}`
        },
        shareBtn() {
            return !(this.$route.name == "Shared")
        }
    },
    methods: {
        like() {
            this.liked = !this.liked
            if(window.localStorage[this.date] == "true") {
                window.localStorage[this.date] = false
            } else {
                window.localStorage[this.date] = true
            }
        },
        share() {
            const el = document.createElement('textarea')  
            el.value = this.link                                 
            el.setAttribute('readonly', '')                
            el.style.position = 'absolute'                     
            el.style.left = '-9999px'                      
            document.body.appendChild(el)                  
            const selected =  document.getSelection().rangeCount > 0  ? document.getSelection().getRangeAt(0) : false                                    
            el.select()                                    
            document.execCommand('copy')                   
            document.body.removeChild(el)                  
            if (selected) {                                 
                document.getSelection().removeAllRanges()   
                document.getSelection().addRange(selected)   
            }
            this.snackbar = true
        }
    },
}
</script>

<style>

</style>