<template>
  <div class="pic-upload">
    <input type="hidden" name="photoimg" :value="photoimg">
    <input type="hidden" name="titlepic" :value="titlepic">
    <input type="hidden" name="link" :value="JSON.stringify(linkCollections)">
    
    <template v-if="!photoimg">
     
    <div class="uploadbtn">
      <form id="imgUpLoad">
        <div class="uploadbtn-btn" @click="upload($event)" >上传图片<input type="file" id="upComm" @change="fileChange($event)"></div>
      </form> 
      <p>上传图片需小于2M,支持PNG,JPG格式</p> 
      <span>请上传高清优美图片，低质量图片无法通过审核哦</span>
    </div> 

    </template>
    <div id="pic-container" class="pic-container" :style="'width:'+width + 'px'">
      <template v-if="photoimg">
        <!-- imgcontainer -->
        <div :style="size">
         <img :src="photoimg">
         <div 
          :style="mask" 
          v-if="maskShow"
          @click="getPos($event)"
         >{{ !addLink ?'请点击图中要增加购买链接的物品':''}}</div>
        </div>
        <!-- imgcontainer -->

        <!-- button -->
        <div style="text-align:right"> 
          <button 
          class="links-add-btn" 
          @click.self.prevent="maskShow = !maskShow"
          >增加图中物品购买地址</button>
        </div>
        <!-- button -->

        <!-- linkbox -->
        <div v-if="addLink" class="link-box" :style="linkBox">
          <div class="link-trangle" :style="trangle"></div>
          <div class="link-edit">
              <textarea name="link" v-model="linkText">{{linkText}}</textarea>
          </div>
          <div class="link-btn">
              <button @click.self.prevent="addLink = !addLink" >取消</button>
              <button @click.self.prevent="linkConfirm">确认</button>
          </div>
        </div>
        <!-- linkbox -->
        
        <!-- link -->
        <template>
          <div class="link-tips" v-for="(data,index) in positionData" :style="data" :id="'link_'+index">
            <div class="link-tips-trangle"></div>
             <p>物品购买地址</p>
          </div>
        </template>
        <!-- link -->
      </template>
    </div>
  </div>
</template>

<script>
export default {
  name: 'upload',
  data() {
    return {
       titlepic : '',
       photoimg : '',
       titlepic:'',
       width : 0,
       height : 0,
       ratio : {},
       maskShow : false, // 显示遮罩层
       addLink : false , //增加链接
       linkBox : { // 链接输入框初始样式
          width:'400px',
          height: '150px',
          top:0,
          left:0
       },
       trangle : '', // 链接三角
       linkText : '', // 输入框输入的链接
       linkCollections:[], // 链接集合
    };
  },
  mounted(){
    // this.photoimg = "http://img.reretu.com/2017/09/15/12fd4c2e118aa138_600x600.jpg"
    // this.getSize()
  },
  methods:{
    upload(e){

      if($user.userid == void 0){
        e.preventDefault();
        MsgBox('请先登录');
        return;
      }   
    },
    fileChange(e){
      let self = this
        $.upload({
          file: e.target.files[0],
          url: '/api/upComm/',
          formData: {
                'timestamp':$user.timestamp,
                'token':$user.vertify,
                'uid':$user.userid,
                'classid':$user.classid 
          },  
          success : function(data){
            var data = JSON.parse(data.responseText);

            self.photoimg = data.url;
            self.titlepic = data.titlepic;
            self.getSize();
            self.$emit('uploadSuccess',{
              bol : true,
              titlepic : data.titlepic
            }); 
          }
        })
    },
    getSize() {
      let img = new Image();
      img.src = this.photoimg;
      img.onload = () => {
        this.width = img.width
        this.height = img.height
      }
    },
    getPos(e) {
      var ratioX = (e.offsetX/this.width).toFixed(4);
      var ratioY = (e.offsetY/this.height).toFixed(4);
      this.addLink = true;
      this.linkBoxCss(e.offsetX, e.offsetY)// 链接框样式
      this.ratio.x = ratioX;
      this.ratio.y = ratioY;
      // console.log(x,y)
    },
    linkConfirm(){
      if(this.pureLinkText == ''){
        MsgBox('请输入商品链接地址');
        return;
      }

      if(!this.checkUrl(this.pureLinkText)) {
        MsgBox('您输入的商品地址不正确');
        return;
      }

       this.linkCollections.push({
          link: this.linkText,
          ratio : this.ratio
       })

       this.addLink = !this.addLink;
       this.maskShow = !this.maskShow;
       this.linkText = '';
       this.ratio = {};
      
    },
    checkUrl( str ) {
       var reg=/(http|https):\/\/[\w\-_]+(\.[\w\-_]+)+([\w\-\.,@?^=%&:/~\+#]*[\w\-\@?^=%&/~\+#])?/;
       return reg.test(str)
    },
    linkBoxCss(x, y) {

      if(x < this.areaX && y < this.areaY) { // 主区域
        this.linkBox.top = y + 20+ 'px';
        this.linkBox.left = x - 15 + 'px';
        this.trangle = 'left:10px;top:-8px;'

      }else if(x > this.areaX && y < this.areaY){ // x轴方向限制
        this.trangle = 'top:-8px;left:'+  (x - this.areaX - 10) + 'px'
        this.linkBox.top = y + 20+ 'px';
        this.linkBox.left = this.areaX + 'px';
      }else if(x < this.areaX && y > this.areaY) { // y 方向限制
        this.linkBox.top = ( y - parseInt(this.linkBox.height) - 20 )+ 'px';
        this.linkBox.left = x - 15 + 'px';
        this.trangle = 'left:10px;bottom:-8px;transform:rotate(180deg)'

      }else if(x > this.areaX && y > this.areaY) { // 
        this.linkBox.top = ( y - parseInt(this.linkBox.height) - 20 )+ 'px';
        this.linkBox.left = this.areaX + 'px';
        this.trangle = 'left:'+ ( x - this.areaX - 10) +'px;bottom:-8px;transform:rotate(180deg)'
      }
    }
  },
  computed:{
    size(){
      return Object.assign({},{
        width : this.width + 'px',
        height : this.height + 'px',
        position:'relative',
      })
    },
    mask(){
     
      let obj = {
        position : 'absolute',
        top : 0,
        left : 0,
        color: '#fff',
        lineHeight: this.height + 'px',
        fontSize : '14px',
        backgroundColor:"rgba(0,0,0,0.5)",
        width : this.width + 'px',
        height : this.height + 'px',
        zIndex :2,
      }
      return Object.assign({},obj)
    },
    areaX(){
      return parseInt(this.width) - parseInt(this.linkBox.width)
    },
    areaY(){
      return parseInt(this.height) - parseInt(this.linkBox.height)
    },
    positionData(){
      return this.linkCollections.reduce((pre,aft) => {
        pre.push({
          top: aft.ratio.y * this.height - 23 + 'px',
          left: aft.ratio.x * this.width + 12 + 'px',
        })
        return pre;
      },[])
    },
    pureLinkText(){
      return this.linkText.replace(/\s+/g,"");
    }
  }
};
</script>
