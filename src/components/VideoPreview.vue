<style scoped>
	video{
		width: 400px;
		margin: auto;
	}
</style>

<template>
	<div class="container">
		<div>
			<h2>Video File Preview</h2>
			<hr/>
			<label>Video File
				<input type="file" accept="video/*" @change="handleFileUpload( $event )"/>
			</label>
			<br>
			<video id="video-preview" controls v-show="file != ''"/>
			<br>
			<button v-on:click="submitFile()">Submit File</button>
		</div>
	</div>
</template>

<script>
	import axios from 'axios';
	
	export default {
		data(){
			return {
				file: ''
			}
		},
		
		methods: {
			handleFileUpload( event ){
				this.file = event.target.files[0];
				this.previewVideo();
			},
			
			previewVideo(){
				let video = document.getElementById('video-preview');
				let reader = new FileReader();

				reader.readAsDataURL( this.file );
				reader.addEventListener('load', function(){
					video.src = reader.result;
				});
			},

			submitFile(){
				let formData = new FormData();
				
				formData.append('file', this.file);
				
				axios.post( '/video-file',
					formData,
					{
						headers: {
							'Content-Type': 'multipart/form-data'
						}
					}
				).then(function(){
					console.log('SUCCESS!!');
				})
				.catch(function(){
					console.log('FAILURE!!');
				});
			}
		}
	}
</script>
	