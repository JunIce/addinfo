<template>
  <div id="app">
    <div class="upload-part">
    <Upload></Upload>
    <Description />
    <Album />
    <Tag />
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
</style>
