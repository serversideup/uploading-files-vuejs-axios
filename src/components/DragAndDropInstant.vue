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
	
	progress{
		width: 400px;
		margin: auto;
		display: block;
		margin-top: 20px;
		margin-bottom: 20px;
	}
</style>

<template>
	<div id="file-drag-drop-instant">
		<h2>Drag And Drop Instant</h2>
		<hr/>
		<form id="drop-form-instant" @drop="handleFileDrop( $event )">
			<span class="drop-files">Drop the files here!</span>
		</form>

		<progress max="100" :value.prop="uploadPercentage"></progress>
		
		<span v-show="uploadPercentage > 0">Uploading {{ uploadPercentage }}%...</span>
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
					document.getElementById('drop-form-instant').addEventListener(evt, function(e){
						e.preventDefault();
						e.stopPropagation();
					}.bind(this), false);
				}.bind(this));
			},
			
			handleFileDrop( event ){
				for( let i = 0; i < event.dataTransfer.files.length; i++ ){
					this.files.push( event.dataTransfer.files[i] );
				}
				
				this.submitFiles();
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
				axios.post( '/file-drag-drop-instant',
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