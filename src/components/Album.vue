<template>
<div>
	<div class="pic-album">
		<h4>选择专辑:</h4>
		<input type="hidden" name="albumid" id="albumid" :value="aid">
		 <span>
		 	<label 
		 	id="pic-album-select"
			@click="listShow = !listShow;"
		 	>{{aname ? aname : '未选择专辑'}}</label> 
		 	<div class="tags-arrowWrap" @click="listShow = !listShow"> <i class="tags-arrow"  :class="{'tags-arrow-up':listShow}"></i></div>
		 </span>
		 <transition name="">
			 <div class="pic-album-list" :style="listShow ? listStyle : {}">
				 <ul>
				 <li @click="listShow = !listShow;aid=aname=''">未选择专辑</li>
				 <li 
				 v-for="album in userAlbum" 
				 :data-aid="album.aid"
				 @click="aid = $event.target.getAttribute('data-aid'); aname = $event.target.innerHTML; listShow = !listShow"
				 >{{album.album_name}}</li>
				 </ul> 
				 <p id="createAlbum" @click="createTip = true; listShow = !listShow">创建专辑</p>
			 </div>
		 </transition>
	</div>
	<template v-if="createTip">
		 	  <div id="showTips"> 
				   <div class="bg-color"></div> 
				   <div class="tipsBox" ref="mybox" > 
				    <div> 
				     <div class="tipsBox-title"> 
				      <h4>创建专辑</h4> 
				     </div> 
				     <div class="tipsBox-main"> 
				      <div class="add-album"> 
				       <div class="create-album"> 
				        <div class="album-list-wrap"> 
				         <div class="tips-album-list" ref="albumLists"> 
				          <span class="on">未选择专辑</span> 
				          <span 
				          v-for="album in userAlbum"
				          @click="boxSelect($event)"
				          :data-aid="album.aid">{{album.album_name}}</span>
				         </div> 
				         <div class="create-album-text"> 
				          <input 
				          type="text" 
				          name="album_name" 
				          placeholder="快速创建专辑"
				          @keyup.13.prevent = "userCreateAlbum"
						  v-model="userAl"
						  :value="userAl"
				          /> 
				          <a href="javascript:;" 
				          id="create-album-btn"
							@click="userCreateAlbum"
				          >创建</a> 
				         </div> 
				        </div> 
				       </div> 
				      </div> 
				     </div> 
				    </div> 
				    <i class="boxClose" @click="createTip = false"></i> 
				   </div> 
				  </div>
		 </template>
	</div>
</template>
<script>
	export default {
		name: 'album',
		
		updated(){
			if(this.createTip) {
				let tipsbox = this.$refs.mybox
				//初始化样式
			 	let winWidth = document.documentElement.clientWidth;
				let winHeight = document.documentElement.clientHeight;
				tipsbox.style.top = (winHeight - tipsbox.clientHeight)/2 + document.body.scrollTop + 'px'
				tipsbox.style.left = (winWidth - tipsbox.clientWidth)/2 + 'px'
			}	
		},
		data() {
			return{
				userAlbum : window.__INITIALSTATE__.useralbum[0].reverse(),
				listShow : false,
				listStyle :{
					opacity : 1,
					zIndex : 1
				},
				aid:'',
				aname: '',
				createTip : false,
				userAl : '',
			}
		},
		methods:{
			userCreateAlbum(){

				if(this.trimUserAl == '') { MsgBox('输入不能为空'); return; }

				if(this.specialSign(this.trimUserAl)) { MsgBox('输入不能含有特殊字符'); return; }

				if(this.isRepeat(this.trimUserAl)) { 
					MsgBox('已经创建过了，重新取个名字吧！');
					 return; 
				}else{
					let self = this
					$.$post('/api/album/',{
						body : 'album_name='+ self.trimUserAl
					}).then(function(res){
						if(res.status == 1) {
							self.userAlbum.unshift({
								aid : res.aid,
								album_name :res.album_name
							})
						}
					})	
				}
				this.userAl = ''
			},
			boxSelect(e){
				let el = e.target;
				
				this.aid = el.getAttribute('data-aid');
				this.aname = el.innerHTML;
				let children = this.$refs.albumLists.childNodes;
				[].slice.call(children).map(child => {
					if(child.nodeType == 1) {
						child.classList.remove('on')
					}
				})
				el.classList.add('on')
			},
			specialSign( val ){
				var pattern = new RegExp("[`~!@#$^&*()=|{}':;',\\[\\].<>/?~！%@#￥……&*（）——|{}【】‘；：”“'。，、？《》]");
				return pattern.test( val )
			},
			isRepeat( val ) {
				return this.userAlbum.find(album => album.album_name == val )
			}
		},
		computed:{
			trimUserAl(){
				return this.userAl.replace(/\s+/g,"");
			}
		}
	}
</script>
<style>
#showTips{width: 100%; height: 100%;}
.bg-color{position: fixed; background-color: #000; filter:alpha(opacity=50); -moz-opacity:0.5; -khtml-opacity: 0.5; opacity: 0.5; width: 100%; height:100%; top:0; left:0; z-index:100;}
.tipsBox{position: absolute; background-color: #fff; border-radius: 4px; z-index:100;}
.tipsBox-title{border-bottom: 1px solid #ccc; padding:15px 36px;}
.tipsBox-title h4{color:#333; font-size:1em; font-weight: 400;margin:0;}
.tipsBox-main{padding: 30px 18px;}
.boxClose {    background: url(../assets/closed.png) no-repeat;    width: 16px;    height: 16px;    background-size: contain;    text-indent: -9999px;    position: absolute;    top: 12px;    right: 12px;    cursor: pointer;
}</style>