<template>
  <div>
    <img class="full-picture" :src="urlForFull" alt="">
    <div class="previews-item">

      <ul>
        <li v-for="pic in dynamicArr" :key="pic">
          <PreviewsItem :pic="pic"
                        :pics="pics"
                        :getImgUrl="getImgUrl"
                        @del-item="del"
                        @show-full="showFullPicture">

          </PreviewsItem>
        </li>
      </ul>
    </div>

    <div class="button-page">
      <button name="button-back-page" @click="navigations" :disabled="firstPage">назад
      </button>
      <span>{{ numberPage }}  из  {{ this.maxPages }}</span>
      <button name="button-forward-page" @click="navigations" :disabled="lastPage">
        вперед
      </button>
    </div>
    <div class="cont">
      <div class="large-12 medium-12 small-12 cell">
        <label>File
          <input type="file" id="file" ref="file" v-on:change="handleFileUpload()"/>
        </label>
      </div>
    </div>
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
      firstPicture: '',
      lastPicture: '',
      firstPage: '',
      lastPage: '',
      dynamicArr: [],
      maxPages: '',
      urlForFull: '',
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
      this.urlForFull = this.getImgUrl(pic);
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
      this.dynamicArr.splice(item, 1);
      this.pics.splice(item, 1);
      this.countMaxPages();
      this.showPage(this.numberPage)
      this.checkPage(this.numberPage);
      // this.checkPage(this.numberPage);
      // this.showPage();
      if (this.dynamicArr.length <= 1) {
        this.numberPage--;
        this.checkPage(this.numberPage);
      }
      console.log(this.dynamicArr)

    },
    showPage(n) {
      console.log(n)
      let arr = [];
      if (n <= 1) {
        arr = this.pics.slice(0, 9);
      } else {
        let count = (n - 1) * 9;
        arr = this.pics.slice(count, count + 9);
      }
      this.dynamicArr = arr;
      this.urlForFull = this.getImgUrl(this.dynamicArr[0]);
    },
    navigations(e) {
      switch (e.target.name) {
        case "button-forward-page":
          this.numberPage += 1;
          this.showPage(this.numberPage)
          this.checkPage(this.numberPage);
          break;
        case "button-back-page":
          this.numberPage -= 1;
          this.showPage(this.numberPage)
          this.checkPage(this.numberPage);
          break;
        case "button-forward-img":
          this.indexImg += 1;
          this.check(this.indexImg);
          break;
        case "button-back-img":
          this.indexImg -= 1;
          this.check(this.indexImg);
          break;
        default:
          return;
      }
    },
    checkPage(el) {
      if (el === 1) {
        this.firstPage = true;
        if (this.maxPages !== 1) {
          this.lastPage = false;
        }
      } else if (el === this.maxPages) {
        this.lastPage = true;
        if (this.maxPages !== 1) {
          this.firstPage = false;
        }
      } else {
        this.firstPage = false;
        this.lastPage = false;
      }
    },
    countMaxPages() {
      let x = this.pics.length % 9;
      if (x > 0) {
        this.maxPages = Math.trunc(this.pics.length / 9) + 1;
      } else this.maxPages = Math.trunc(this.pics.length / 9)
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

.full-picture {
  position: absolute;
  margin: 50px auto 0;
  width: 1452px;
  height: 726px;
  left: 234px;

  background-color: rgba(102, 102, 102, 0.5);
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
</style>