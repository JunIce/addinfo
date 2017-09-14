<template>
	<div class="pic-album">
		<h4>选择专辑:</h4>
		 <span>
		 	<label id="pic-album-select">未选择专辑</label> 
		 	<div class="tags-arrowWrap"><i class="tags-arrow"></i></div>
		 </span>
		 <transition name="">
			 <div class="pic-album-list">
				 <ul>
				 <li>未选择专辑</li>
				 <li v-for="album in userAlbum" :data-aid="album.aid">{{album.album_name}}</li>
				 </ul> 
				 <p id="createAlbum">创建专辑</p>
			 </div>
		 </transition>
	</div>
</template>
<script>
	export default {
		name: 'album',
		data() {
			return{
				userAlbum : window.__INITIALSTATE__.useralbum[0]
			}
		}
	}
</script>