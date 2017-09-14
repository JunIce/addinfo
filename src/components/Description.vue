<template>
	<div id="pic-title" class="pic-title">
	   <h4>添加描述:</h4> 
	   <div class="describe-title">
	   <textarea name="title" 
	   id="describe" 
	   :placeholder="msg"
	   v-model="describe"
		 @blur="msg = describe ? describe : '准确的图片描述，会让更多人关注~~'"
		 @focus="msg = describe ? describe : ''"
	   ></textarea>
	   </div>
	</div>
</template>

<script>
export default {
  name: 'description',
  data() {
    return {
    	describe:'',
    	msg : '准确的图片描述，会让更多人关注~~'
    };
  }
};
</script>