<style scoped>
	form{
		display: block;
		height: 400px;
		width: 400px;
		background: #ccc;
		margin: auto;
		margin-top: 40px;
		text-align: center;
		line-height: 400px;
		border-radius: 4px;
	}
	
	div.file-listing{
		width: 400px;
		margin: auto;
		padding: 10px;
		border-bottom: 1px solid #ddd;
	}
	
	div.file-listing img{
		height: 100px;
		display: block;
	}
	
	div.remove-container{
		text-align: center;
	}
	
	div.remove-container a{
		color: red;
		cursor: pointer;
	}
	
	a.submit-button{
		display: block;
		margin: auto;
		text-align: center;
		width: 200px;
		padding: 10px;
		text-transform: uppercase;
		background-color: #CCC;
		color: white;
		font-weight: bold;
		margin-top: 20px;
	}
	
	progress{
		width: 400px;
		margin: auto;
		display: block;
		margin-top: 20px;
		margin-bottom: 20px;
	}
</style>

<template>
	<div id="file-drag-drop">
		<h2>Drag And Drop</h2>
		<hr/>
		<form id="drop-form" @drop="handleFileDrop( $event )">
			<span class="drop-files">Drop the files here!</span>
		</form>

		<div v-for="(file, key) in files"
				v-bind:key="'file-'+key" 
				class="file-listing">
				<img class="preview" v-bind:id="'drag-and-drop-preview-'+parseInt( key )"/>
				{{ file.name }}
				<div class="remove-container">
					<a class="remove" v-on:click="removeFile( key )">Remove</a>
				</div>
		</div>
		
		<progress max="100" :value.prop="uploadPercentage"></progress>
		
		<a class="submit-button" v-on:click="submitFiles()" v-show="files.length > 0">Submit</a>
	</div>
</template>

<script>
	import axios from 'axios';
	
	export default {
		data(){
			return {
				dragAndDropCapable: false,
				files: [],
				uploadPercentage: 0
			}
		},
		
		mounted(){
			/*
				Determine if drag and drop functionality is capable in the browser
			*/
			this.dragAndDropCapable = this.determineDragAndDropCapable();
		
			/*
				If drag and drop capable, then we continue to bind events to our elements.
			*/
			if( this.dragAndDropCapable ){
				this.bindEvents();
			}
		},
		
		methods: {
			bindEvents(){
				/*
					Listen to all of the drag events and bind an event listener to each
					for the fileform.
				*/
				['drag', 'dragstart', 'dragend', 'dragover', 'dragenter', 'dragleave', 'drop'].forEach( function( evt ) {
					/*
						For each event add an event listener that prevents the default action
						(opening the file in the browser) and stop the propagation of the event (so
						no other elements open the file in the browser)
					*/
					document.getElementById('drop-form').addEventListener(evt, function(e){
						e.preventDefault();
						e.stopPropagation();
					}.bind(this), false);
				}.bind(this));
			},
			
			handleFileDrop( event ){
				for( let i = 0; i < event.dataTransfer.files.length; i++ ){
					this.files.push( event.dataTransfer.files[i] );
				}
				
				this.getImagePreviews();
			},
			
			determineDragAndDropCapable(){
				/*
					Create a test element to see if certain events
					are present that let us do drag and drop.
				*/
				var div = document.createElement('div');
			
				/*
					Check to see if the `draggable` event is in the element
					or the `ondragstart` and `ondrop` events are in the element. If
					they are, then we have what we need for dragging and dropping files.
			
					We also check to see if the window has `FormData` and `FileReader` objects
					present so we can do our AJAX uploading
				*/
				return ( ( 'draggable' in div )
								|| ( 'ondragstart' in div && 'ondrop' in div ) )
								&& 'FormData' in window
								&& 'FileReader' in window;
			},
			
			getImagePreviews(){
				/*
					Iterate over all of the files and generate an image preview for each one.
				*/
				for( let i = 0; i < this.files.length; i++ ){
					/*
						Ensure the file is an image file
					*/
					if ( /\.(jpe?g|png|gif)$/i.test( this.files[i].name ) ) {
						/*
							Create a new FileReader object
						*/
						let reader = new FileReader();
			
						/*
							Add an event listener for when the file has been loaded
							to update the src on the file preview.
						*/
						reader.addEventListener("load", function(){
							document.getElementById('drag-and-drop-preview-'+parseInt(i)).src = reader.result;
						}.bind(this), false);
			
						/*
							Read the data for the file in through the reader. When it has
							been loaded, we listen to the event propagated and set the image
							src to what was loaded from the reader.
						*/
						reader.readAsDataURL( this.files[i] );
					}else{
						/*
							We do the next tick so the reference is bound and we can access it.
						*/
						this.$nextTick(function(){
							document.getElementById('drag-and-drop-preview-'+parseInt(i)).src = '/images/file.png';
						});
					}
				}
			},
			
			removeFile( key ){
				this.files.splice( key, 1 );
				this.getImagePreviews();
			},
			
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
					Make the request to the POST /file-drag-drop URL
				*/
				axios.post( '/file-drag-drop',
					formData,
					{
						headers: {
								'Content-Type': 'multipart/form-data'
						},
						onUploadProgress: function( progressEvent ) {
							this.uploadPercentage = parseInt( Math.round( ( progressEvent.loaded / progressEvent.total ) * 100 ) );
						}.bind(this)
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