<template>
  <div class="main-container">
    <div class="full-size-picture">
      <a href="#">
        <img class="leftArrow" @click="navigations" :class="{disabled: firstPicture}"
             src="../assets/icon/Arrow-left.png" alt="error" id="button-back-img">
      </a>
      <div class="full-picture">
        <img :src="urlForFull" alt="error">
      </div>
      <a href="#">
        <img class="rightArrow" @click="navigations" :class="{disabled: lastPicture}"
             src="../assets/icon/Arrow-right.png" alt="error" id="button-forward-img"
        >
      </a>
    </div>

    <div class="previews-item">
      <ul>
        <li v-for="pic in dynamicArr" :key="pic">
          <PreviewsItem :pic="pic"
                        :pics="pics"
                        :getImgUrl="getImgUrl"
                        :indexForActive="indexForActive"
                        @del-item="del"
                        @show-full="showFullPicture">
            <input type="radio">
          </PreviewsItem>
        </li>
      </ul>
    </div>

    <div class="button-page">
      <button id="button-back-page" @click="navigations" :disabled="firstPage">назад
      </button>
      <span>{{ numberPage }}  из  {{ maxPages }}</span>
      <button id="button-forward-page" @click="navigations" :disabled="lastPage">
        вперед
      </button>
    </div>
    <!--    <div class="cont">-->
    <!--      <div class="large-12 medium-12 small-12 cell">-->
    <!--        <label>File-->
    <!--          <input type="file" id="file" ref="file" v-on:change="handleFileUpload()"/>-->
    <!--        </label>-->
    <!--      </div>-->
    <!--    </div>-->
  </div>
</template>

<script>

import PreviewsItem from "@/components/previews-item";

export default {
  name: "gallery",
  components: {
    PreviewsItem,
  },
  data() {
    return {
      pics: [],
      numberPage: 1,
      indexImg: '',
      firstPicture: true,
      lastPicture: false,
      firstPage: '',
      lastPage: '',
      dynamicArr: [],
      maxPages: '',
      urlForFull: '',
      indexForActive: '',
    }
  },
  methods: {
    getIllustrations() {
      const illustrations = require.context(
          '@/assets/img',
          true, /^.*\.jpg$/)
      this.pics = illustrations.keys().map(item => item.slice(2,))
      if (this.pics.length >= 9) {
        this.lastPage = false;
      }
    },
    getImgUrl(img) {
      return require('@/assets/img/' + img);
    },
    showFullPicture(pic) {
      this.indexImg = this.dynamicArr.indexOf(pic);
      this.urlForFull = this.getImgUrl(pic);
      this.checkImg();
    },
    // handleFileUpload() {
    //   // const img = new Image();
    //   // const vm = this;
    //   // vm.img = this.$refs.file.files[0]
    //   // this.pics.push(vm.img);
    //   // console.log(this.pics);
    //
    //   const image = new Image();
    //   // const reader = new FileReader();
    //   image.src = this.$refs.file.files[0].name;
    //   this.pics.push(image.src);
    //   // reader.readAsDataURL(file);
    // },
    del(item) {
      let a = this.dynamicArr.splice(item, 1);
      this.pics.splice(item, 1);

      if (a.length < 1) {
        this.numberPage--;
      }
      this.showPage(this.numberPage);
      this.checkPage(this.numberPage);
      this.countMaxPages();

      if (this.pics.length === 0) {
        this.urlForFull = require('@/assets/icon/Missingimage.png')
      }
    },
    showPage(n) {
      if (this.pics.length === 0) {
        return;
      }
      let arr = [];
      if (n <= 1) {
        arr = this.pics.slice(0, 9);
      } else {
        let count = (n - 1) * 9;
        arr = this.pics.slice(count, count + 9);
      }
      // this.lastPicture = false;
      // this.firstPicture = true;
      this.indexImg = 0;
      this.dynamicArr = arr;
      this.urlForFull = this.getImgUrl(this.dynamicArr[0]);
      this.indexForActive = this.pics.indexOf(this.dynamicArr[0])
      this.checkPage(this.numberPage);
      this.checkImg();
    },
    checkImg() {
      if (this.dynamicArr.length === 1) {
        this.firstPicture = true;
        this.lastPicture = true;
      } else if (this.indexImg === this.dynamicArr.length - 1) {
        this.firstPicture = false;
        this.lastPicture = true;
      } else if (this.indexImg === 0) {
        this.firstPicture = true;
        this.lastPicture = false;
      } else {
        this.lastPicture = false;
        this.firstPicture = false;
      }
    },

    checkPage(page) {
      if (this.pics.length <= 9) {
        this.firstPage = true;
        this.lastPage = true;
      } else if (page === 1) {
        this.firstPage = true;
        this.lastPage = false;
      } else if (page === this.maxPages) {
        this.firstPage = false;
        this.lastPage = true;
      } else {
        this.firstPage = false;
        this.lastPage = false;
      }
    },
    navigations(e) {
      switch (e.target.id) {
        case "button-forward-page":
          this.numberPage += 1;
          this.showPage(this.numberPage)
          // this.checkPage(this.numberPage);
          break;
        case "button-back-page":
          this.numberPage -= 1;
          this.showPage(this.numberPage)
          // this.checkPage(this.numberPage);
          break;
        case "button-forward-img":
          // this.indexImg += 1;
          // this.checkImg();
          this.showFullPicture(this.dynamicArr[this.indexImg + 1])

          // console.log(this.indexImg)
          // this.check(this.indexImg);
          break;
        case "button-back-img":
          // this.indexImg -= 1;
          this.showFullPicture(this.dynamicArr[this.indexImg - 1])
          // console.log(this.indexImg)
          // this.check(this.indexImg);
          break;
        default:
          return;
      }
    },
    countMaxPages() {
      let x = this.pics.length / 9;
      let y = this.pics.length % 9;

      if (y > 0) {
        this.maxPages = Math.trunc(x) + 1;
      } else if (x === 0) {
        this.maxPages = 1
      } else this.maxPages = Math.trunc(x)
    }
  },
  mounted() {
    this.getIllustrations();
    this.countMaxPages();
    this.showPage(this.numberPage);
  }
}
</script>

<style scoped>
div, ul, li, button, img {
  margin: 0;
  padding: 0;
}

.main-container {
  width: 1920px;
  height: 1200px;
}

.full-picture {
  position: absolute;
  margin: 50px auto 0;
  width: 1452px;
  height: 726px;
  left: 234px;
  display: flex;
  justify-content: center;
}

.full-picture img {
  position: absolute;
  height: 726px;
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
  justify-content: flex-start;
}

li {
  list-style-type: none;
  margin-right: 24px;
}

.container > button {
  position: absolute;
  top: 100px;
  width: 300px;
  height: 100px;
  font-size: 30px;
}

.button-page {
  position: relative;
  height: 100px;
  width: 1000px;
  top: 500px;
  margin: auto;
  display: flex;
  justify-content: space-between;
}

.button-page > button {
  width: 300px;
}

.full-size-picture {
  position: absolute;
  width: 100%;
  display: flex;
  justify-content: space-between;
  /*left: 0;*/
}

.leftArrow {
  display: flex;
  align-items: center;
  cursor: pointer;
  transition: 0.3s;

  /*top: 300px;*/
  /*left: 50px;*/
}

a > .disabled {
  display: none;
}

.leftArrow, .rightArrow {
  width: 50px;
}

.rightArrow {
  grid-area: rightArrow;
  display: flex;
  align-items: center;
  cursor: pointer;
  transition: 0.3s;
  /*top: 300px;*/
  /*right: 50px;*/

  /*.end {*/
  /*  display: none;*/
  /*}*/
}
</style>