<script>
import { toPng, toJpeg, toBlob, toPixelData, toSvg } from 'html-to-image';
import download from 'downloadjs'
import { createElementBlock } from '@vue/runtime-core';

export default{
  data () {
    return {
      availableRatios: [
        {
          label: "Square",
          class: 'aspect-square',
        },
        {
          label: "4:5",
          class: 'aspect-[4/5]'
        },
        {
          label: '1.91:1',
          class: 'aspect-[1.91/1] max-800'
        }
      ],
      metaData:{
        watermark: {
          text: '',
          image: null,
          color: 'text-white/70',
        },
        title: '',
        subtitle: '',
        ratio: 0
      },
      images:[
      ]
    }
  },
  methods:{
    appendImage(ImageFile){
      let previewUrl = URL.createObjectURL(ImageFile)
      let image = {
        file: ImageFile,
        preview: previewUrl,
      }
      console.log(image);
      this.images.push(image);
    },
    handleFilesLoad(event){
      console.log(event.target.files);
      if(event.target.files.length > 0){
        for (const file of event.target.files) {
          this.appendImage(file)
        }
      }
    },
    removeItem(index){
      this.images.splice(index, 1);
    },
    downloadAll(){
      console.log(this.$refs);
      Object.keys(this.$refs).forEach((key)=> {
        this.$refs[key][0].style.borderRadius = '0px';
        toBlob(this.$refs[key][0])
        .then((dataUrl) => {
          this.$refs[key][0].style.borderRadius = null
          console.log(dataUrl);
          download(dataUrl, `pack/${this.metaData.title}-${key}`)
        })
        .catch(function (error) {
          console.error('oops, something went wrong!', error);
        });
      })
    }
  }
}
</script>

<template>
  <section class="preview-section pattern-grid-sm flex flex-col">
    <!-- <div class="container mx-auto px-4 "> -->
      <div class="slider flex grow flex-row gap-5 overflow-scroll h-full items-center py-10">
        <template v-if="images.length > 0">
          <template v-for="(item, index) in images" :key="index">
            <div :ref="`ImageNode-${index}`" class="group preview-image shrink-0 w-96 relative bg-slate-900 overflow-hidden rounded-2xl border-slate-300 shadow-sm z-10 p-4 text-slate-100" :class="availableRatios[metaData.ratio].class">
              <img :src="item.preview" class="flex absolute inset-0 h-full w-full object-cover blur-sm transition-all" loading="lazy" @load="$event.target.classList.remove('blur-sm')">
              <div class="absolute flex flex-col top-0 left-0 right-0 p-6 pb-20 bg-gradient-to-b from-black/90 to-transparent" v-if="index == 0">
                <span class="text-2xl font-semibold">{{ metaData.title }}</span>
                <span class="text-base font-light">{{ metaData.subtitle }}</span>
              </div>
              <div class="absolute flex flex-col bottom-0 right-0 p-3 pr-">
                <span class="text-sm font-semibold" :class="metaData.watermark.color">{{ metaData.watermark.text }}</span>
              </div>
              <button class="close-btn print:invisible" @click.prevent="removeItem(index)">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
                </svg>
              </button>
            </div>
          </template>
          <div class="w-96 shrink-0" :class="availableRatios[metaData.ratio].class">
            <label for="dropzone-file" class="flex flex-col items-center justify-center w-full h-full border-2 border-slate-200 border-dashed rounded-lg cursor-pointer bg-slate-50 dark:hover:bg-bray-800 dark:bg-slate-700 hover:bg-slate-100 dark:border-slate-600 dark:hover:border-slate-500 dark:hover:bg-slate-600">
                <div class="flex flex-col items-center justify-center pt-5 pb-6">
                    <svg aria-hidden="true" class="w-10 h-10 mb-3 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"></path></svg>
                    <p class="mb-2 text-sm text-slate-500 dark:text-slate-400"><span class="font-semibold">Click to upload</span> or drag and drop</p>
                    <p class="text-xs text-slate-500 dark:text-slate-400">SVG, PNG, JPG or GIF</p>
                </div>
                <input id="dropzone-file" type="file" accept="images/*" multiple class="hidden" @change="handleFilesLoad" />
            </label>
          </div> 
        </template>
        <template v-else>
          <div class="flex items-center justify-center w-full">
            <label for="dropzone-file" class="flex flex-col items-center justify-center w-full h-80 border-2 border-slate-200 border-dashed rounded-lg cursor-pointer bg-slate-50 dark:hover:bg-bray-800 dark:bg-slate-700 hover:bg-slate-100 dark:border-slate-600 dark:hover:border-slate-500 dark:hover:bg-slate-600">
                <div class="flex flex-col items-center justify-center pt-5 pb-6">
                    <svg aria-hidden="true" class="w-10 h-10 mb-3 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"></path></svg>
                    <p class="mb-2 text-sm text-slate-500 dark:text-slate-400"><span class="font-semibold">Click to upload</span> or drag and drop</p>
                    <p class="text-xs text-slate-500 dark:text-slate-400">SVG, PNG, JPG or GIF</p>
                </div>
                <input id="dropzone-file" type="file" accept="images/*" multiple class="hidden" @change="handleFilesLoad" />
            </label>
          </div> 
        </template>
      </div>
    <!-- </div> -->
  </section>
  <section class="py-10 pb-32">
    <main class="container mx-auto px-4">
      <div class="grid grid-cols-12 gap-8 sm:gap-4 w-full">
        <div class="sm:col-span-6 col-span-12 sm:gap-4 flex flex-col gap-5">
          <label for="email" class="block flex flex-col gap-2 text-sm font-medium text-slate-900 dark:text-white">
            Title
            <input type="email" id="email" v-model="metaData.title" class="bg-slate-50 border border-slate-300 text-slate-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-slate-700 dark:border-slate-600 dark:placeholder-slate-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" 
              placeholder="Item title" required>
          </label>
          <label for="email" class="block flex flex-col gap-2 text-sm font-medium text-slate-900 dark:text-white">
            Subtitle
            <input type="email" id="email" v-model="metaData.subtitle" class="bg-slate-50 border border-slate-300 text-slate-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-slate-700 dark:border-slate-600 dark:placeholder-slate-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" 
              placeholder="Item title" required>
          </label>
          <div class="grid grid-cols-12 gap-8 sm:gap-4 w-full">
            <div class="col-span-6">
              <label for="email" class="block flex flex-col gap-2 text-sm font-medium text-slate-900 dark:text-white">
                Watermark
                <input type="email" id="email" v-model="metaData.watermark.text" class="bg-slate-50 border border-slate-300 text-slate-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-slate-700 dark:border-slate-600 dark:placeholder-slate-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" 
                  placeholder="Store Name" required>
              </label>
            </div>
            <div class="col-span-6">
              <label for="email" class="block flex flex-col gap-2 text-sm font-medium text-slate-900 dark:text-white">
                Watermark Color
                <select v-model="metaData.watermark.color" class="bg-slate-50 border border-slate-300 text-slate-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-slate-700 dark:border-slate-600 dark:placeholder-slate-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500">
                  <option value="color-wm-white">White</option>
                  <option value="color-wm-black">Black</option>
                </select>
              </label>
            </div>
          </div>
        </div>
        <div class="sm:col-span-6 col-span-12 flex flex-col gap-5">
          <div class="grid grid-cols-12 gap-8 sm:gap-4 w-full">
            <div class="sm:col-span-6 col-span-12">
              <label class="block flex flex-col gap-2 text-sm font-medium text-slate-900 dark:text-white">
                Aspect ratio
                <select v-model="metaData.ratio" class="bg-slate-50 border border-slate-300 text-slate-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-slate-700 dark:border-slate-600 dark:placeholder-slate-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500">
                  <template v-for="(item, index) in availableRatios" :key="index">
                    <option :value="index">{{item.label}}</option>
                  </template>
                </select>
              </label>
            </div>
            <div class="sm:col-span-6 col-span-12 pt-7">
              <button type="button" @click="downloadAll"
              class="w-full text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 mr-2 mb-2 dark:bg-blue-600 dark:hover:bg-blue-700 focus:outline-none dark:focus:ring-blue-800">Download All</button>
            </div>
          </div>
        </div>
      </div>
    </main>
  </section>
</template>

<style lang="scss" scoped>
.preview-section{
  @apply min-h-[500px] bg-slate-100 dark:bg-slate-800 relative text-slate-200 dark:text-slate-700 isolate dark:border-t-2 dark:border-b-[1px] dark:border-t-slate-800 dark:border-b-slate-800;
  &::after{
    content: '';
    @apply absolute top-0 left-0 right-0 h-10 bg-gradient-to-b from-slate-50 dark:from-slate-900 to-transparent pointer-events-none z-0;
  }
  &::before{
    content: '';
    @apply absolute bottom-0 left-0 right-0 h-10 bg-gradient-to-t from-slate-50 dark:from-slate-900 to-transparent pointer-events-none z-0;
  }
}
.slider{
  padding-inline: calc((100vw - var(--tw-breakpoint))/2 + 1rem);
}
.preview-image{
  .close-btn{
    will-change: backrop-filter;
    @apply -translate-y-full opacity-0 invisible transition-all absolute top-5 right-5 w-10 h-10 rounded-full backdrop-blur-sm flex justify-center items-center hover:bg-slate-50/20;
  }
  &:hover{
    .close-btn{
      @apply translate-y-0 opacity-100 visible
    }
  }
}

.color-wm-white{
  @apply text-slate-50/70;
}
.color-wm-black{
  @apply text-black/70;
}

.max-800{
  max-width: 800px;
  width: 100%;
}

</style>