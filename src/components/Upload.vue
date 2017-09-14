<template>
  <div class="pic-upload"><div class="uploadbtn"><form id="imgUpLoad"><div class="uploadbtn-btn">
                            上传图片
                         <input type="file" id="upComm"></div></form> <p>上传图片需小于2M,支持PNG,JPG格式</p> <span>请上传高清优美图片，低质量图片无法通过审核哦</span></div> <div id="pic-container" class="pic-container"></div></div>
</template>

<script>
export default {
  name: 'upload',
  data() {
    return {

    };
  },
};
</script>
