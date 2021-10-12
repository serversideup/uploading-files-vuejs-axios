<template>
	<div class="container">
		<div>
			<h2>Audio File Preview</h2>
			<hr/>
			<label>Audio File
				<input type="file" accept="audio/*" @change="handleFileUpload( $event )"/>
			</label>
			<br>
			<audio id="audio-preview" controls v-show="file != ''"/>
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
				this.previewAudio();
			},
			
			previewAudio(){
				let audio = document.getElementById('audio-preview');
				let reader = new FileReader();

				reader.readAsDataURL( this.file );
				reader.addEventListener('load', function(){
					audio.src = reader.result;
				});
			},

			submitFile(){
				let formData = new FormData();
				
				formData.append('file', this.file);
				
				axios.post( '/audio-file',
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
	