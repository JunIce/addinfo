<template>
	<div class="pic-tags">
	<h4>添加标签:</h4>
	<input name="tags" type="hidden" id="tags" :value="tagsSubmit">
	<div class="pic-tags-select">
		<a 
		v-for="tag in tagsSelect" 
		:data-tid="tag.tagid"
		@click="removeTag($event)"
		class="remo-tags"
		>{{tag.tagname}}</a>

		<div class="tagsInputBox">
		<input 
			type="text" 
			placeholder="输入标签" 
			id="userTags" 
			autocomplete="off"
			@keyup.13.stop.prevent="userTags($event.target)"
		/> 
		</div>
	</div> 
	<div class="pic-tags-list cl">
		<a 
		v-for="tag,index in isgood" 
		:style="tag.check ? hasCheck : {}"
		:data-tid="tag.tagid"
		@click="selectTag($event,index)"
		>{{tag.tagname}}</a>
		<template  name="fold">
			<div class="child-tags" :style="childTag" v-if="child.length > 0 && showChild">
			<div id="popWrap"><div id="c-trangle"></div> 
			<a href="javascript:;" 
			v-for="tag in child"
			:data-tid="tag.tagid"
			:style="tag.check ? hasCheck : {}"
			@click="selectTag($event)"
			>{{tag.tagname}}</a>
			<div class="checkSub"><button @click="showChild = false">确定</button></div>
			</div> 
			<i class="closepop" @click="showChild = false"></i>
			</div>
		</template>
	</div>
	</div>
</template>
<script>
	export default {
		name : 'tagList',
		data(){
			let list = window.__INITIALSTATE__.recomend;
			return{
				recomendTags : list,
				relationships : this.relation(list),
				tagsCollection : this.collection(list),
				child : [],
				childTag : {},
				showChild : true,
				hasCheck :{
					backgroundColor:'#ff6666',
					color:'#fff'
				},
				userInput:[]
			}
		},
		methods:{
			selectTag(e , index){
				let tagid = e.target.getAttribute('data-tid');
				if(this.tagsSelect.length >= 5 ) {
					MsgBox('最多选择5个标签')
					return;
				}
				let tag = this.select(tagid);
				tag.check = true
				index != void 0 ? (()=>{
					this.child = this.getChild(tagid)
					this.showChild = true
					this.childTag = {
						top: e.target.offsetTop + 49 + 'px',
						left : e.target.offsetLeft + 'px'
					}
				})(e) : null // 用index来辨识是来自父元素或者子元素
			},
			removeTag(e){
				let tagid = e.target.getAttribute('data-tid');
				let tagname = e.target.value;

				if(tagid != void 0) { // 原始推荐标签
					let tag = this.select(tagid);
					tag.check = false
				}else{ // 用户输入标签
					let index = this.userInput.indexOf(tagname)
					this.userInput.splice(index,1)
				}
			},
			userTags(el){
				if(el.value == '') {
					MsgBox('请输入标签名');
					return;
				}
				if(this.pureTag(el.value)) {
					MsgBox('不能包含特殊字符');
					return;
				}
				//个数限制
				if(this.tagsSelect.length >= 5 ) {
					MsgBox('最多选择5个标签')
					return;
				}

				let value = el.value.replace(/\s+/g,"");
				//遍历所有标签
				let res = this.tagsCollection.filter(tag => tag.tagname == value)

				if(res.length > 0) {
					res[0].check = true
				}else{
					let uRes = this.userInput.filter(tag => tag.tagname == value)

					if(uRes.length > 0) {
						return
					}else{
						this.userInput.push({tagname:value})
					}
				}
				
				el.value = ''
			},
			select(tid) { // 根据元素id 返回元素对象
				return this.tagsCollection.find(tag => tag.tagid == tid)
			},
			relation(arr){
				return arr.reduce((pre,aft) => {
					var list = aft.rela_tags;
					pre[aft.tagid] = list ? list.map(tag => tag.tagid ) : [] 
					return pre
				},{})
			},
			collection(arr) {
				let arrs = arr.reduce((pre,aft) => {
					return aft['rela_tags'] ? pre.concat(aft['rela_tags']) : pre
				} ,arr)

				return arrs.map(tag => Object.assign({},{check : false},tag))
			},
			getChild(tid) {
				let child = this.relationships[tid];
				//返回子元素数组
				return child ? child.reduce((pre,aft) => {
					pre.push(this.select(aft))
					return pre
				},[]) : []
			},
			pureTag ( val ) {
				let pattern = new RegExp("[`~!@#$^&*()=|{}':;',\\[\\].<>/?~！%@#￥……&*（）——|{}【】‘；：”“'。，、？《》]");
				return pattern.test( val );
			}
		},
		computed:{
			tagsSelect(){ //选择的标签
				let select = this.tagsCollection.filter(tag =>tag.check == true)
				let allTags = select.concat(this.userInput)
				return allTags
			},
			isgood(){ // 推荐标签
				let isgood = this.tagsCollection.filter(tag =>tag.isgood == 1)
				return isgood
			},
			tagsSubmit(){
				return this.tagsSelect.reduce((pre,aft)=>{
					pre.push(aft.tagname)
					return pre
				},[])
			},

		}
	}
</script>
<style>
.fold-enter-active {
  animation-name: fold-in;
  animation-duration: .5s;
}
.fold-leave-active {
  animation-name: fold-out;
  animation-duration: .5s;
}
@keyframes fold-in {
  0% {
    transform: translate3d(0, 100%, 0);
  }
  50% {
    transform: translate3d(0, 50%, 0);
  }
  100% {
    transform: translate3d(0, 0, 0);
  }
}
@keyframes fold-out {
  0% {
    transform: translate3d(0, 0, 0);
  }
  50% {
    transform: translate3d(0, 50%, 0);
  }
  100% {
    transform: translate3d(0, 100%, 0);
  }
}
</style>
