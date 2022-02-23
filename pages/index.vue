<template>
  <div class="mx-auto w-2/3">
    <div class="aspect-w-16 aspect-h-6 overflow-hidden">
      <video id="video" class="w-full bg-gray-500"></video>
    </div>
    <div>
      <div v-if="liveStream" class="text-center">
        <button 
          class="inline-flex items-center m-3 px-2.5 py-1.5 border border-gray-300 shadow-sm text-xs font-medium rounded text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500" 
          v-if="!videoShowing"
          @click="startVideo"
        >
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
            <path d="M2 6a2 2 0 012-2h6a2 2 0 012 2v8a2 2 0 01-2 2H4a2 2 0 01-2-2V6zM14.553 7.106A1 1 0 0014 8v4a1 1 0 00.553.894l2 1A1 1 0 0018 13V7a1 1 0 00-1.447-.894l-2 1z" />
          </svg>
        </button>
        <button 
          class="inline-flex items-center m-3 px-2.5 py-1.5 border border-transparent shadow-sm text-sm leading-4 font-medium rounded-md text-white bg-red-600 hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500"
          v-else
          @click="stopVideo"
        >
          <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 10l4.553-2.276A1 1 0 0121 8.618v6.764a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z" />
          </svg>
        </button>
      </div>
      <div class="text-center">
        <button 
          class="inline-flex items-center m-3 px-2.5 py-1.5 border border-gray-300 shadow-sm text-xs font-medium rounded text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500" 
          v-if="!liveStream && !initializing" 
          @click="initializeStream"
        >
          Initialize Stream
        </button>
        <button 
          class="inline-flex items-center m-3 px-2.5 py-1.5 border border-gray-300 shadow-sm text-xs font-medium rounded text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500" 
          v-if="liveStream && !streaming" 
          @click="startStream"
        >
          Start Streaming
        </button>
        <button 
          class="inline-flex items-center m-3 px-2.5 py-1.5 border border-transparent shadow-sm text-sm leading-4 font-medium rounded-md text-white bg-red-600 hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500"
          v-else-if="liveStream"
          @click="stopStream"
        >
          Stop Streaming
        </button>
      </div>
    </div>
    
    <div class="my-5 flex flex-col">
      <div class="-my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
        <div class="py-2 align-middle inline-block min-w-full sm:px-6 lg:px-8">
          <div class="shadow overflow-hidden border-b border-gray-200 sm:rounded-lg">
            <table class="min-w-full divide-y divide-gray-200">
              <tbody>

                <!-- Even row -->
                <tr class="bg-gray-50">
                  <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                    Video
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                    {{ videoShowing ? "On" : "Off" }}
                  </td>
                </tr>

                <!-- Odd row -->
                <tr class="bg-white">
                  <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                    Status
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                    {{
                      liveStream 
                      ? (streaming ? "Streaming" : "Not streaming")
                      : (initializing ? "Initializing" : "Not initialized")
                    }}
                  </td>
                </tr>

                <!-- Even row -->
                <tr class="bg-gray-50">
                  <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                    Public ID
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                    {{ liveStream ? liveStream.response.public_id : ""}}
                  </td>
                </tr>
                
                <!-- Odd row -->
                <tr class="bg-white">
                  <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                    Watch
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                    <a v-if="liveStream" target="_blank" :href="`/watch/${liveStream.response.public_id}`">
                      Watch stream online
                      <svg class="inline-block h-4 w-4" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14" />
                      </svg>
                    </a>
                  </td>
                </tr>
                
                <!-- Even row -->
                <tr class="bg-gray-50">
                  <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                    Stream URL
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                    {{ liveStream ? streamURL : ""}}
                  </td>
                </tr>
                
                <!-- Odd row -->
                <tr class="bg-white">
                  <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                    File URL
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                    {{ liveStream  ? fileURL : ""}}
                  </td>
                </tr>
              
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  data(){
    return {
      cloudinaryJsStreaming:null,
      videoElement: null,
      initializing:false,
      liveStream: false,
      streaming:false,
      videoShowing:false,
    };
  },

  computed:{
    fileURL(){
      return this.liveStream 
        ? 'https://res.cloudinary.com/' + process.env.NUXT_ENV_CLOUDINARY_CLOUD_NAME + '/video/upload/' + this.liveStream.response.public_id + '.mp4'
        : null;
    },
    streamURL(){
      return this.liveStream
        ? 'https://res.cloudinary.com/'+ process.env.NUXT_ENV_CLOUDINARY_CLOUD_NAME + '/video/upload/' + this.liveStream.response.public_id + '.m3u8'
        : null;
    },
  },

  methods:{
    async initializeStream(){
      this.initializing = true;
      if(
          !process.env.NUXT_ENV_CLOUDINARY_CLOUD_NAME || 
          !process.env.NUXT_ENV_CLOUDINARY_STREAMING_UPLOAD_PRESET
        ){
        this.initializing = false;
        return;
      }

      this.videoElement = document.getElementById("video");
      
      this.cloudinaryJsStreaming = cloudinaryJsStreaming;

      this.liveStream = await this.cloudinaryJsStreaming.initLiveStream({
          cloudName: process.env.NUXT_ENV_CLOUDINARY_CLOUD_NAME,
          uploadPreset: process.env.NUXT_ENV_CLOUDINARY_STREAMING_UPLOAD_PRESET,
          debug: "all",
          hlsTarget: true,
          fileTarget: true,
          events: {
              start: function (args) {
                console.log("start",args);
              },
              stop: function (args) {
                console.log("stop",args);
              },
              error: function(error){
                console.log("error",error);
              },
              local_stream: stream => this.attachStream(stream)
          }
      });

      this.initializing = false;
    },

    startStream(){
      this.liveStream.start(this.liveStream.response.public_id);
    },
    stopStream(){
      this.liveStream.stop();
      this.streaming = false;
    },
    attachStream(stream) {
      this.liveStream.attach(this.videoElement, stream);
      this.videoElement.play();
      this.streaming = true;
      this.videoShowing = true;
    },
    startVideo(){
      this.cloudinaryJsStreaming
      .attachCamera(this.videoElement)
      .then(() => {
        this.videoElement.play();
        this.videoShowing = true;
        });
    },
    stopVideo(){
      this.cloudinaryJsStreaming
      .detachCamera(this.videoElement,false)
      .then(() => this.videoShowing = false);
    },

  }
}
</script>
