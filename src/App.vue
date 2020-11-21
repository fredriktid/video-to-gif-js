<template>
    <div class="antialiased text-gray-900 px-6">
        <div class="max-w-7xl mx-auto py-12">
            <div class="text-center">
                <h1 class="text-4xl text-white font-semibold">
                    Video-to-GIF
                </h1>
            </div>
            <div
                v-if="!isSupported"
                class="text-xl text-center mt-5"
            >
                Your browser does not support ffmpeg.wasm. Try in Chrome instead.
            </div>
            <div
                v-else
                class="grid grid-cols-1 md:grid-cols-2 gap-4 mt-6 mb-5"
            >
                <div :class="`bg-white rounded md:shadow p-4 ${!videoSrc && 'h-60'} flex flex-col items-center justify-center`">
                    <video
                        v-if="videoSrc"
                        controls
                        :src="videoSrc"
                        class="mb-4"
                    />
                    <input
                        ref="fileInput"
                        type="file"
                        class="hidden"
                        accept="video/*"
                        @change="updatePreview"
                    >
                    <div>
                        <Button
                            :disabled="!isReady || isConverting"
                            :text="isReady ? 'Select Video File' : 'Loading ffmpeg...'"
                            @click="$refs.fileInput.click()"
                        />
                        <Button
                            v-if="videoSrc"
                            class="ml-2"
                            :disabled="isConverting"
                            :text="isConverting ? 'Converting Video...' : 'Convert To GIF'"
                            @click="convertToGIF(video)"
                        />
                    </div>
                </div>
                <div class="bg-white rounded md:shadow p-4">
                    <div class="flex items-center justify-center h-full">
                        <div
                            v-if="imageSrc"
                            class="text-center"
                        >
                            <img
                                :src="imageSrc"
                                alt="output gif"
                                class="mb-4"
                            >
                            <Button
                                text="Download GIF"
                                @click="downloadBlob"
                            />
                        </div>
                        <div
                            v-else
                            class="text-gray-500 text-xl"
                        >
                            GIF comes here
                        </div>
                    </div>
                </div>
            </div>
            <div class="text-center">
                <p class="text-lg text-white">
                    Made with
                    <a
                        href="https://github.com/ffmpegwasm/ffmpeg.wasm"
                        class="underline"
                    >ffmpeg.wasm</a>,
                    <a
                        href="https://vuejs.org/"
                        class="underline"
                    >Vue.js</a>, and
                    <a
                        href="https://tailwindcss.com/"
                        class="underline"
                    >Tailwind CSS</a>
                </p>
            </div>
        </div>
    </div>
</template>

<script>
import { ref } from 'vue';
import useFFmpeg from './hooks/useFFmpeg';
import Button from './components/Button.vue';

export default {
  name: 'App',
  components: { Button },
  setup() {
    const videoSrc = ref(null);
    const video = ref(null);

    const {
      isReady,
      convertToGIF,
      output: imageSrc,
      isConverting,
      isSupported,
    } = useFFmpeg();

    const updatePreview = (e) => {
      if (e.target.files.length) {
        videoSrc.value = URL.createObjectURL(e.target.files[0]);
        video.value = e.target.files[0];
      }
    };

    const downloadBlob = () => {
      const saveImg = document.createElement('a');
      saveImg.href = imageSrc.value;
      saveImg.download = `${Date.now()}.gif`;
      saveImg.click();
    };

    return {
      videoSrc,
      updatePreview,
      imageSrc,
      isConverting,
      isReady,
      convertToGIF,
      downloadBlob,
      video,
      isSupported,
    };
  },
};
</script>
