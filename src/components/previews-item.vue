<template>
  <div class="container">
    <div class="box"
         :class="this.indexForActive === this.pics.indexOf(this.pic)? 'active':''"
         @click="showFullPic"></div>
    <img class="previews-img" :src="getImgUrl(this.pic)" alt="error">
    <div class="delete">
      <img src="../assets/icon/delete.png" :name="this.pics.indexOf(this.pic)"
           alt="delete icon" @click="delItem"/>
    </div>
  </div>
</template>

<script>

export default {
  name: "previews-item",
  props: {
    pics: Array,
    pic: String,
    getImgUrl: Function,
    indexForActive: Number,
  },
  methods: {
    showFullPic() {
      this.$emit("show-full", this.pic);
    },
    delItem(e) {
      this.$emit("del-item", e.target.name);
    },
  },
}
</script>

<style scoped>
.container {
  width: 140px;
  height: 140px;
  position: relative;
}

.box {
  width: 140px;
  height: 140px;
  position: absolute;
  background-color: rgba(102, 102, 102, 0.5);
}

.active {
  opacity: 0;
}

.container:hover .delete {
  opacity: 1;
}

.previews-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center;
}

.delete {
  width: 140px;
  height: 40px;
  display: flex;
  opacity: 0;
  flex-direction: row-reverse;
  align-items: center;
  position: absolute;
  top: 100px;
  background-color: rgba(102, 102, 102, 0.5);
  transition: 0.3s;
}

img {
  margin-right: 10px;
  cursor: pointer;
}

</style>