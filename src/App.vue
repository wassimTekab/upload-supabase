<template>
<h1>Test Upload</h1>
<SimpleUpload/>
<hr>
<MultipleUpload/>
</template>

<script>
import SimpleUpload from './components/SimpleUpload.vue';
import MultipleUpload from './components/MultipleUpload.vue'
export default {
  name: "App",
  components:{
    SimpleUpload,
    MultipleUpload
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
