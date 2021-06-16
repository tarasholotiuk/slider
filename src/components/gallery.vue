<template>
  <div>
    <FullPicture></FullPicture>
    <div class="previews-item">
      <ul>
        <li v-for="pic in pics.slice(0,9)" :key="pic">
          <PreviewsItem :pic="pic" :pics="pics" @del-item="del"></PreviewsItem>
        </li>
      </ul>
    </div>
    <div class="container">
      <div class="large-12 medium-12 small-12 cell">
        <label>File
          <input type="file" id="file" ref="file" v-on:change="handleFileUpload()"/>
        </label>
      </div>
    </div>
  </div>
</template>

<script>

import FullPicture from "@/components/full-picture";
import PreviewsItem from "@/components/previews-item";

export default {
  name: "gallery",
  components: {
    PreviewsItem,
    FullPicture
  },
  data() {
    return {
      pics: [],
    }
  },
  methods: {
    getIllustrations() {
      const illustrations = require.context(
          '@/assets/img',
          true, /^.*\.jpg$/)
      this.pics = illustrations.keys().map(item => item.slice(2,))
    },
    handleFileUpload() {
      // const img = new Image();
      // const vm = this;
      // vm.img = this.$refs.file.files[0]
      // this.pics.push(vm.img);
      // console.log(this.pics);

      const image = new Image();
      // const reader = new FileReader();
      image.src = this.$refs.file.files[0].name;
      this.pics.push(image.src);
      // reader.readAsDataURL(file);
    },
    del(item) {
      this.pics.splice(item, 1);
      console.log(this.pics)
    }
  },
  mounted() {
    this.getIllustrations();
  }

}
</script>

<style scoped>
div {
  text-align: center;
}
div, ul, li, button, img {
  margin: 0;
  padding: 0;
}

.previews-item {
  margin: 0;
  padding: 0;
  top: 796px;
  left: 234px;
  position: relative;
  width: 1452px;
  height: 140px;

}

ul {

  display: flex;
  justify-content: space-between;
}

li {
  list-style-type: none;
}

button {
  position: absolute;
  top: 100px;
  width: 300px;
  height: 100px;
  font-size: 30px;
}

</style>