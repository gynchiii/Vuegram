<template>
  <q-page class="constrain-more q-pa-md">
<div class="camera-frame q-pa-md">
  <video
   v-show="!imageCaptured"
   ref="video"
   class="full-width"
   autoplay   
   playsinline    
 />
   <canvas
   v-show="imageCaptured"
   ref="canvas"
   class="full-width"
   height="240"
   />
  </div>
 
<div class="text-center q-pa-md">
   <q-btn
      @click="captureImage"
      v-if="hasCameraSupport"
      size="lg"
      round
      color="grey-10"
      icon="eva-camera" />
<div class="row justify-center q-ma-md">
   <q-input
      v-model="post.caption"
      class="col col-sm-6"
      label="Caption"
      dense />
</div>
      <div class="row justify-center q-ma-md">
         <q-input
           v-model="post.location"
           class="col col-sm-6"
           label="Location"
           dense>
           <template v-slot:append>
         <q-btn
          round
          dense
          flat
          icon="eva-navigation-2" />        
          </template>
         </q-input>
      </div>
       <div class="row justify-center q-mt-lg">
         <q-btn
           unelevated
           rounded
           color="primary"
           label="Post Image" />
      </div>
  </div>
  </q-page>
</template>

<script>

require('md-gum-polyfill')
import { uid } from 'quasar';

export default {
  name: 'PageCamera',
  data() {
    return {
      post: {
        id: uid(),
        caption: '',
        location: '',
        photo: null,
        date: Date.now()
      },
      imageCaptured: false,
      imageUpload:[],
      hasCameraSupport: true
    }
  },
  methods: {
    initCamera() {
    navigator.mediaDevices.getUserMedia({
      video:true
    }).then(stream => {
       this.$refs.video.srcObject = stream
    }).catch(error => {
      this.hasCameraSupport = false 
    })

    },
    captureImage() {
    let video = this.$refs.video
    let canvas = this.$refs.canvas
    canvas.width = video.getBoundingClientRect().width
    canvas.height = video.getBoundingClientRect().height
    let context = canvas.getContext('2d')
    context.drawImage(video, 0, 0, canvas.width, canvas.height)
    this.imageCaptured = true
    this.post.photo = canvas.toDataURL()
    },
    //transform in large base64 to posts photo
    dataURItoBlob(dataURI) {
      let byteString = atob(dataURI.split(',')[1]);

      let mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0]

      let arbuff = new ArrayBuffer(byteString.length)

      let intr = new Uint8Array(arbuff)

      for(let i = 0; i < byteString.length; i++) {
        intr[i] = byteString.charCodeAt(i);
      }

      let blob = new Blob([arbuff], {type: mimeString})
      
      return blob; //blob blob 
    }
  },
     mounted() {
       this.initCamera()
     }
  }
</script>

