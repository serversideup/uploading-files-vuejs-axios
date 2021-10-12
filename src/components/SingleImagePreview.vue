<style scoped>
	div.container img{
		max-width: 200px;
		max-height: 200px;
		display: block;
		margin: auto;
		margin-top: 20px;
		margin-bottom: 20px;
	}
</style>

<template>
	<div class="container">
		<h2>Single Image Preview</h2>
		<hr/>
		<div class="large-12 medium-12 small-12 cell">
			<label>File Preview
				<input type="file" id="file" accept="image/*" @change="handleFileUpload( $event )"/>
			</label>
			<img v-bind:src="imagePreview" v-show="showPreview"/>
			<button v-on:click="submitFile()">Submit</button>
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
				file: '',
				showPreview: false,
				imagePreview: ''
			}
		},

		methods: {
			/*
				Submits the file to the server
			*/
			submitFile(){
				/*
					Initialize the form data
				*/
				let formData = new FormData();

				/*
					Add the form data we need to submit
				*/
				formData.append('file', this.file);

				/*
					Make the request to the POST /single-file URL
				*/
				axios.post( '/single-image-preview',
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
				Handles a change on the file upload
			*/
			handleFileUpload( event ){
				/*
					Set the local file variable to what the user has selected.
				*/
				this.file = event.target.files[0];

				/*
					Initialize a File Reader object
				*/
				let reader  = new FileReader();

				/*
					Add an event listener to the reader that when the file
					has been loaded, we flag the show preview as true and set the
					image to be what was read from the reader.
				*/
				reader.addEventListener("load", function () {
					this.showPreview = true;
					this.imagePreview = reader.result;
				}.bind(this), false);

				/*
					Check to see if the file is not empty.
				*/
				if( this.file ){
					/*
						Ensure the file is an image file.
					*/
					if ( /\.(jpe?g|png|gif)$/i.test( this.file.name ) ) {
						/*
							Fire the readAsDataURL method which will read the file in and
							upon completion fire a 'load' event which we will listen to and
							display the image in the preview.
						*/
						reader.readAsDataURL( this.file );
					}
				}
			}
		}
	}
</script>