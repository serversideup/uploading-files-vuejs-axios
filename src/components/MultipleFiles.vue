<template>
	<div class="container">
		<div>
			<h2>Multiple Files</h2>
			<hr/>
			<label>Files
				<input type="file" multiple @change="handleFileUploads( $event )"/>
			</label>
			<br>
			<button v-on:click="submitFiles()">Submit</button>
		</div>
	</div>
</template>

<script>
	import axios from 'axios';
	
	export default {
		data(){
			return {
				files: []
			}
		},
		
		methods: {
			handleFileUpload( event ){
				this.files = event.target.files;
			},
			
			submitFile(){
				let formData = new FormData();
				
				for( var i = 0; i < this.files.length; i++ ){
					let file = this.files[i];
				
					formData.append('files[' + i + ']', file);
				}
				
				axios.post( '/multiple-files',
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
	