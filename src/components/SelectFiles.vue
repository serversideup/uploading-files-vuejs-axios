<style scoped>
	input[type="file"]{
		position: absolute;
		top: -500px;
	}

	div.file-listing{
		width: 400px;
		margin: auto;
	}

	span.remove-file{
		color: red;
		cursor: pointer;
		float: right;
	}
</style>

<template>
	<div class="container">
		<div class="large-12 medium-12 small-12 cell">
			<h2>Select Files</h2>
			<hr/>
			<label for="select-files">Files
				<input id="select-files" type="file" multiple @change="handleFilesUpload( $event )"/>
			</label>
		</div>
		<div class="large-12 medium-12 small-12">
			<div 
				v-for="(file, key) in files" 
				v-bind:key="'file-'+key"
				class="file-listing">{{ file.name }} <span class="remove-file" v-on:click="removeFile( key )">Remove</span>
			</div>
		</div>
		<br>
		<div class="large-12 medium-12 small-12 cell">
			<button v-on:click="addFiles()">Add Files</button>
		</div>
		<br>
		<div class="large-12 medium-12 small-12 cell">
			<button v-on:click="submitFiles()">Submit</button>
		</div>
	</div>
</template>

<script>
	import axios from 'axios';
	
	export default {
		/*
			Defines the data used by the component
		*/
		data(){
			return {
				files: []
			}
		},

		/*
			Defines the method used by the component
		*/
		methods: {
			/*
				Adds a file
			*/
			addFiles(){
				document.getElementById('select-files').click();
			},

			/*
				Submits files to the server
			*/
			submitFiles(){
				/*
					Initialize the form data
				*/
				let formData = new FormData();

				/*
					Iterate over any file sent over appending the files
					to the form data.
				*/
				for( var i = 0; i < this.files.length; i++ ){
					let file = this.files[i];

					formData.append('files[' + i + ']', file);
				}

				/*
					Make the request to the POST /select-files URL
				*/
				axios.post( '/select-files',
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
			},

			/*
				Handles the uploading of files
			*/
			handleFilesUpload( event ){
				let uploadedFiles = event.target.files;

				/*
					Adds the uploaded file to the files array
				*/
				for( var i = 0; i < uploadedFiles.length; i++ ){
					this.files.push( uploadedFiles[i] );
				}
			},

			/*
				Removes a select file the user has uploaded
			*/
			removeFile( key ){
				this.files.splice( key, 1 );
			}
		}
	}
</script>