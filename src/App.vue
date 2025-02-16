<template>
  <div class="relative isolate overflow-hidden bg-white min-h-screen p-6 flex flex-col items-center justify-center">
    <div class="absolute inset-x-0 -top-40 -z-10 transform-gpu overflow-hidden blur-3xl sm:-top-80" aria-hidden="true">
        <div class="relative left-[calc(50%-11rem)] aspect-[1155/678] w-[36.125rem] -translate-x-1/2 rotate-[30deg] bg-gradient-to-tr from-[#ff8080] to-[#4f39f6] opacity-30 sm:left-[calc(50%-30rem)] sm:w-[72.1875rem]" style="clip-path: polygon(74.1% 44.1%, 100% 61.6%, 97.5% 26.9%, 85.5% 0.1%, 80.7% 2%, 72.5% 32.5%, 60.2% 62.4%, 52.4% 68.1%, 47.5% 58.3%, 45.2% 34.5%, 27.5% 76.7%, 0.1% 64.9%, 17.9% 100%, 27.6% 76.8%, 76.1% 97.7%, 74.1% 44.1%)" />
      </div>
    <svg class="absolute inset-0 -z-10 size-full stroke-gray-100 [mask-image:radial-gradient(100%_100%_at_bottom_right,white,transparent)]" aria-hidden="true">
      <defs>
        <pattern id="0787a7c5-978c-4f66-83c7-11c213f99cb7" width="200" height="200" x="50%" y="-1" patternUnits="userSpaceOnUse">
          <path d="M.5 200V.5H200" fill="none" />
        </pattern>
      </defs>
      <rect width="100%" height="100%" stroke-width="0" fill="url(#0787a7c5-978c-4f66-83c7-11c213f99cb7)" />
    </svg>
    <div class="p-6 text-center">
      
      <div class="">
    <!-- Title -->
    <h1 class="mb-6 text-4xl font-extrabold leading-none tracking-tight text-gray-900 md:text-5xl lg:text-6xl "><mark class="px-2 pb-1 text-white bg-indigo-600 rounded-sm ">Image</mark> Compressor</h1>
<p class="text-lg font-normal text-gray-500 lg:text-xl mb-4">Best Image Compression Tool: Easily Reduce File Size Without Sacrificing Quality</p>

    <!-- FilePond Section -->
    <div class="w-full max-w-3xl bg-white p-6 rounded-lg shadow mb-6">
      <FilePond
        name="filepond"
        ref="pond"
        :files="pondFiles"
        :allow-multiple="true"
        :allow-file-type-validation="true"
        :accepted-file-types="['image/png', 'image/jpeg', 'image/jpg', 'image/webp']"
        :allow-image-preview="false"
        :image-preview-height="120"
        :allow-image-resize="false"
        :max-files="20"
        label-idle='<div class="flex flex-col items-center justify-center pt-5 pb-6">
            <svg class="w-8 h-8 mb-4 text-gray-500 " aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 20 16">
                <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 13h3a3 3 0 0 0 0-6h-.025A5.56 5.56 0 0 0 16 6.5 5.5 5.5 0 0 0 5.207 5.021C5.137 5.017 5.071 5 5 5a4 4 0 0 0 0 8h2.167M10 15V6m0 0L8 8m2-2 2 2"/>
            </svg>
            <p class="mb-2 text-sm text-gray-500 "><span class="font-semibold">Click to upload</span> or drag and drop</p>
            <p class="text-xs text-gray-500 ">JPEG, PNG, JPG ... (up to 20 files)</p>
        </div>'
        @updatefiles="handleUpdateFiles"
      />
    </div>

    <!-- Compression Settings -->
    <div v-if="images.length" class="w-full max-w-3xl bg-white p-6 pb-4 rounded-lg shadow mb-6">
      <h2 class="text-xl font-semibold mb-3">Compression Settings</h2>
      <div class="md:grid md:grid-cols-2">
         <!-- Method Selector -->
      <div class="flex flex-col space-x-6 mb-6">
        <label class="flex items-center text-gray-700 cursor-pointer">
          <input
            type="radio"
            name="method"
            value="byQuality"
            v-model="compressionMethod"
            class="mr-2 text-indigo-500"
          />
          By Quality
        </label>
        <label class="flex items-center text-gray-700 cursor-pointer">
          <input
            type="radio"
            name="method"
            value="bySize"
            v-model="compressionMethod"
            class="mr-2 text-indigo-500 bg-gray-100 border-gray-300"
          />
          By Max File Size (MB)
        </label>
      </div>

      <!-- If by Quality, show slider (0.1 to 1.0) -->
      <div v-if="compressionMethod === 'byQuality'" class="mb-4">
        <label class="block text-sm font-semibold text-gray-700 mb-2">
          Quality (0.1 to 1.0)
        </label>
        <input
          type="range"
          min="0.1" max="1" step="0.1"
          v-model.number="quality"
          class="w-full"
        />
        <p class="text-xs text-gray-600">Selected Quality: {{ quality }}</p>
      </div>

      <!-- If by Size, show numeric input for MB -->
      <div v-else class="mb-4">
        <label class="block text-sm font-semibold text-gray-700 mb-2">
          Max File Size (MB)
        </label>
        <input
          type="number"
          min="0.1" step="0.1"
          v-model.number="maxSizeMB"
          class="border p-2 rounded w-full"
        />
        <p class="text-sm text-gray-600 mt-2">
          Selected Max Size: {{ maxSizeMB }} MB
        </p>
      </div>
      </div>
     
    </div>

    <!-- Preview & Info Section (only when there's something to show) -->
    <div
      v-if="images.length && (downloadReady || isProcessing)"
      class="w-full max-w-3xl"
    >
      <h2 class="text-xl font-semibold mb-4">Images to Compress</h2>
      <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
        <div
          v-for="(image, index) in images"
          :key="index"
          :class="[
            image.compressedSize !== null ? 'bg-green-50 border border-green-300' : 'bg-white',
            'rounded-lg shadow p-2 flex items-center transition-colors text-left'
          ]"
        >
          <img
            :src="image.preview"
            alt="Preview"
            :class="[
              image.compressedSize !== null ? 'border-green-200' : 'border-gray-200',
              'bg-white w-14 h-14 object-contain mr-4 rounded-lg border p-1 transition-colors'
            ]"
          />
          <div class="flex-grow">
            <p class="text-xs text-gray-600">
              Original: {{ formatBytes(image.size) }}
            </p>
            <p v-if="image.compressedSize !== null" class="text-xs text-gray-600">
              Optimized: {{ formatBytes(image.compressedSize) }}
            </p>
            <p v-if="image.compressedSize !== null" class="text-xs text-green-700 font-semibold">
              Reduction: {{ computePercentageReduction(image.size, image.compressedSize) }}%
            </p>
          </div>
        </div>
      </div>
    </div>

    <!-- Action Buttons -->
    <div v-if="images.length" class="flex flex-wrap items-center justify-center mt-6">
      <!-- Compress Button: Shown if not processing and download is not ready -->
      <ActionButton
        v-if="!isProcessing && !downloadReady"
        label="Compress Images"
        :onClick="compressAll"
        variant="primary"
      />

      <!-- Download Button: Shown if download is ready and not processing -->
      <ActionButton
        v-if="downloadReady && !isProcessing"
        label="Download Compressed Images"
        :onClick="downloadImages"
        variant="success"
      >
        <template #icon>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            class="mr-2 -ml-1 size-5"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M9 8.25H7.5a2.25 2.25 0 0 0-2.25 2.25v9a2.25 2.25 0 0 0 2.25 2.25h9a2.25 2.25 0 0 0 2.25-2.25v-9a2.25 2.25 0 0 0-2.25-2.25H15M9 12l3 3m0 0 3-3m-3 3V2.25"
            />
          </svg>
        </template>
      </ActionButton>
    </div>

    <!-- Processing Status -->
    <div v-if="isProcessing" class="mt-4">
      <ActionButton disabled label="Processing…" variant="disabled">
        <!-- Provide a spinner icon via a named slot -->
        <template #icon>
          <svg
            class="mr-3 -ml-1 size-5 animate-spin text-white"
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
          >
            <circle
              class="opacity-25"
              cx="12"
              cy="12"
              r="10"
              stroke="currentColor"
              stroke-width="4"
            ></circle>
            <path
              class="opacity-75"
              fill="currentColor"
              d="M4 12a8 8 0 0 1 8-8V0C5.373 0 0 5.373 0 12h4zm2 
                 5.291A7.962 7.962 0 0 1 4 12H0c0 
                 3.042 1.135 5.824 3 7.938l3-2.647z"
            ></path>
          </svg>
        </template>
      </ActionButton>

      <p class="text-lg font-semibold text-gray-700">
        Processing... {{ processedCount }}/{{ images.length }}
      </p>
    </div>
  </div>
    
    </div>
  </div>
  <link rel="stylesheet" href="https://rsms.me/inter/inter.css" />
  
</template>

<script setup>
/**
 * Vue 3 + vue-filepond + browser-image-compression + Tailwind
 * With options to compress by quality OR by max file size.
 */
import { ref, reactive } from 'vue';
import ActionButton from "./components/ActionButton.vue";

// 1. Import FilePond core + plugins
import vueFilePond from 'vue-filepond';
import 'filepond/dist/filepond.min.css';                                
import 'filepond-plugin-image-preview/dist/filepond-plugin-image-preview.css';
import FilePondPluginFileValidateType from 'filepond-plugin-file-validate-type';
import FilePondPluginImagePreview from 'filepond-plugin-image-preview';
import FilePondPluginImageResize from 'filepond-plugin-image-resize';

// Register the FilePond plugins
const FilePond = vueFilePond(
  FilePondPluginFileValidateType,
  FilePondPluginImagePreview,
  FilePondPluginImageResize
);

// 2. Other libs
import imageCompression from 'browser-image-compression';
import JSZip from 'jszip';
import { saveAs } from 'file-saver';

// 3. Reactive states
const pondFiles = ref([]); 
const images = reactive([]);

const isProcessing = ref(false);
const processedCount = ref(0);
const downloadReady = ref(false);

// --- NEW: compressionMethod can be 'byQuality' or 'bySize'
const compressionMethod = ref('byQuality');
// For "byQuality", we use initialQuality
const quality = ref(0.8);
// For "bySize", we use maxSizeMB
const maxSizeMB = ref(2.0);

// 4. FilePond handlers
const handleUpdateFiles = (fileItems) => {
  // Clear existing images
  images.splice(0, images.length);

  // Populate from FilePond
  fileItems.forEach((item) => {
    const file = item.file;
    if (!file) return;
    const fileURL = URL.createObjectURL(file);
    images.push({
      file,
      preview: fileURL,
      size: file.size,
      compressed: null,
      compressedSize: null,
    });
  });

  processedCount.value = 0;
  downloadReady.value = false;
};

// 5. Compression
const compressAll = async () => {
  isProcessing.value = true;
  processedCount.value = 0;
  downloadReady.value = false;

  for (let i = 0; i < images.length; i++) {
    const img = images[i];
    try {
      const options = {
        useWebWorker: true,
        maxWidthOrHeight: 4000, 
      };

      // Depending on the user's chosen method:
      if (compressionMethod.value === 'byQuality') {
        // Compress by a specified quality (0.1 - 1.0)
        options.initialQuality = quality.value;
      } else {
        // Compress by a target max size in MB
        // (imageCompression will reduce quality until it hits that size or can’t reduce further)
        options.maxSizeMB = maxSizeMB.value;
      }

      const compressedBlob = await imageCompression(img.file, options);
      img.compressed = compressedBlob;
      img.compressedSize = compressedBlob.size;
    } catch (error) {
      console.error('Compression error:', error);
    } finally {
      processedCount.value++;
    }
  }

  isProcessing.value = false;
  downloadReady.value = true;
};

// 6. Download
const downloadImages = async () => {
  if (images.length === 1) {
    // Single file => no need for zip
    const img = images[0];
    const blobToDownload = img.compressed || img.file;
    saveAs(blobToDownload, `compressed_${img.file.name}`);
  } else {
    // Multiple => zip them
    const zip = new JSZip();
    for (let i = 0; i < images.length; i++) {
      const img = images[i];
      const blobToDownload = img.compressed || img.file;
      zip.file(`compressed_${img.file.name}`, blobToDownload);
    }
    const zipBlob = await zip.generateAsync({ type: 'blob' });
    saveAs(zipBlob, 'compressed_images.zip');
  }
};

// 7. Utility functions
const formatBytes = (bytes, decimals = 2) => {
  if (bytes === 0) return '0 Bytes';
  const k = 1024;
  const dm = decimals < 0 ? 0 : decimals;
  const sizes = ['Bytes','KB','MB','GB','TB'];
  const i = Math.floor(Math.log(bytes) / Math.log(k));
  return parseFloat((bytes / Math.pow(k, i)).toFixed(dm)) + ' ' + sizes[i];
};

const computePercentageReduction = (original, compressed) => {
  if (!compressed || compressed >= original) {
    return 0;
  }
  const diff = original - compressed;
  return ((diff / original) * 100).toFixed(2);
};
</script>
