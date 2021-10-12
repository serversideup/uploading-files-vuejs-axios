<template>
  <img alt="Vue logo" src="./assets/logo.png">
	<single-file/>
	<multiple-files/>
	<select-files/>
	<file-progress/>
	<drag-and-drop/>
	<drag-and-drop-instant/>
	<single-image-preview/>
	<multiple-images-preview/>
	<audio-preview/>
	<video-preview/>
	<csv-preview/>
	<preview-csv/>
</template>

<script>
import SingleFile from './components/SingleFile.vue'
import MultipleFiles from './components/MultipleFiles.vue';
import SelectFiles from './components/SelectFiles.vue';
import FileProgress from './components/FileProgress.vue';
import DragAndDrop from './components/DragAndDrop.vue';
import DragAndDropInstant from './components/DragAndDropInstant.vue';
import SingleImagePreview from './components/SingleImagePreview.vue';
import MultipleImagesPreview from './components/MultipleImagesPreview.vue';
import AudioPreview from './components/AudioPreview.vue';
import VideoPreview from './components/VideoPreview.vue';
import PreviewCsv from './components/PreviewCsv.vue';

export default {
  name: 'App',
  components: {
    SingleFile,
	MultipleFiles,
	SelectFiles,
	FileProgress,
	DragAndDrop,
	DragAndDropInstant,
	SingleImagePreview,
	MultipleImagesPreview,
	AudioPreview,
	VideoPreview,
	PreviewCsv
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
	max-width: 600px;
	margin: auto;
}
</style>
