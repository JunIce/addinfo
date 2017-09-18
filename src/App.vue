<template>
  <div id="app">
    <div class="upload-part">
    <form ref="forms" name="add" method="POST" enctype="multipart/form-data" action="/e/DoInfo/set.php" @submit.prevent >
      <Upload @uploadSuccess="change"></Upload>
      <template v-if="success">
        <Description />
        <Album :titlepic="titlepic"/>
        <Tag />
        <button 
        class="pic-sub"
        @click="submit" 
        >提交分享</button>
      </template>
      </form>
    </div>
  </div>
</template>

<script>
import Upload from './components/Upload';
import Description from './components/Description';
import Album from './components/Album';
import Tag from './components/Tag';

export default {
  name: 'app',
  data(){
    return{
       success: false,
       titlepic : ''
    }
  },
  methods:{
    change(msg){
      this.success = msg.bol,
      this.titlepic = msg.titlepic
    },
    submit(){
      if(this.titlepic){
        this.$refs.forms.submit()
      }else{
        MsgBox('请填写上传信息')
      }
    }
  },
  components: {
    Upload,
    Description,
    Album,
    Tag
  },
};

</script>

<style>
body{
    background: #ededed;
    position: relative;
    font-family: "microsoft yahei","PingFang SC",PingFangRegular,"Heiti SC","San Francisco",Helvetica;
}
body,ul,li,p,div,i,textarea,h4{    margin: 0;
    padding: 0;
    font-family: "Microsoft yahei";}
</style>
