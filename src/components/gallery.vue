<template>
  <div class="main-container">
    <div class="full-size-picture">
      <div class="leftArrow">
        <img @click="navigations" :class="{disabled: firstPicture}"
             src="../assets/icon/Arrow-left.png" alt="error" id="button-back-img">
      </div>
      <div class="full-picture">
        <img :src="urlForFull" alt="error">
      </div>
      <div class="rightArrow">
        <img @click="navigations" :class="{disabled: lastPicture}"
             src="../assets/icon/Arrow-right.png" alt="error" id="button-forward-img"
        >
      </div>
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

    <div class="navigation-page">
      <div class="leftArrow">
        <img @click="navigations" :class="{disabled: firstPage}"
             src="../assets/icon/Arrow-left.png" alt="error" id="button-back-page">
      </div>
      <span>{{ numberPage }}  / {{ maxPages }}</span>
      <div class="rightArrow">
        <img @click="navigations" :class="{disabled: lastPage}"
             src="../assets/icon/Arrow-right.png" alt="error" id="button-forward-page"
        >
      </div>
    </div>
    <div class="upload-img">
      <a href="#">
        <input type="file" id="file-from-PC" ref="file" accept="image/*"
               @change="handleFileUpload()"/>
        Upload new image</a>
      <a href="#" @click="uploadFromFlickr()">Upload from Flickr</a>
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
      if (img.toString().startsWith("https://") || img.toString().startsWith("data:image/"))
        return img;
      return require('@/assets/img/' + img);
    },
    showFullPicture(pic) {
      this.indexImg = this.dynamicArr.indexOf(pic);
      this.urlForFull = this.getImgUrl(pic);
      this.checkImg();
    },

    handleFileUpload() {
      const self = this;
      const reader = new FileReader();
      reader.onload = function (e) {
        self.pics.unshift(e.target.result);
      };
      reader.readAsDataURL(this.$refs.file.files[0]);
      console.log(this.pics)
      this.showPage(this.numberPage);
    },
    async uploadFromFlickr() {
      let url;
      do {
        const randomNumber = Math.floor(Math.random() * 100);
        const response = await fetch("https://api.flickr.com/services/rest/?method=flickr.photos.search&format=json&nojsoncallback=1&api_key=9c0b191a1d8415714a70a2a3db4abdeb&extras=url_m&text=nature");
        const data = await response.json();
        url = data.photos.photo[randomNumber].url_m;
        console.log(url);
      } while (this.pics.includes(url))
      this.pics.unshift(url);
      this.showPage(this.numberPage);
    },

    del(item) {
      console.log(item);
      this.pics.splice(item, 1);
      // let a = this.dynamicArr.splice(item, 1);
      // console.log(a);
      this.showPage(this.numberPage);
      // if (this.dynamicArr.length === 0) {
      //   this.numberPage--;
      // }
      if (this.pics.length === 0) {
        this.numberPage = 1;
        this.urlForFull = require('@/assets/icon/Missingimage.png');
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
      this.indexImg = 0;
      this.dynamicArr = arr;
      this.urlForFull = this.getImgUrl(this.dynamicArr[0]);
      this.indexForActive = this.pics.indexOf(this.dynamicArr[0]);
      this.countMaxPages();
      this.checkPage(this.numberPage);
      this.checkImg();
console.log(this.dynamicArr);
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
          this.numberPage++;
          this.showPage(this.numberPage)
          break;
        case "button-back-page":
          this.numberPage--;
          this.showPage(this.numberPage)
          break;
        case "button-forward-img":
          this.showFullPicture(this.dynamicArr[this.indexImg + 1])
          break;
        case "button-back-img":
          this.showFullPicture(this.dynamicArr[this.indexImg - 1])
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

a, span {
  font-family: Arial, sans-serif;
}

.main-container {
  position: relative;
  width: 1920px;
  height: 1200px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.full-size-picture {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 726px;
  margin-top: 50px;
}

.full-picture {
  width: 1452px;
  height: 100%;
  text-align: center;
}

.full-picture img {
  height: 100%;
  /*width: 100%;*/
}

.leftArrow, .rightArrow {
  width: 41px;
  height: 70px;
  margin: 0 20px;
}

.leftArrow > img, .rightArrow > img {
  width: 41px;
  height: 70px;
  cursor: pointer;
}

.previews-item {
  margin-top: 20px;
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

.navigation-page {
  height: 70px;
  width: 373px;
  margin-top: 20px;
  display: flex;
  justify-content: space-between;
}

.navigation-page > span {
  font-size: 42px;
  font-weight: bold;
  line-height: 70px;
}

div > .disabled {
  display: none;
  cursor: default;
}

.upload-img {
  height: 70px;
  width: 100%;
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

.upload-img > a {
  width: 400px;
  height: 100%;
  background-color: #252525;
  color: white;
  border-radius: 35px;
  margin: 0 25px;
  text-decoration: none;
  line-height: 100%;
  text-align: center;
  align-content: center;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 32px;
}

#file-from-PC {
  position: absolute;
  width: 400px;
  height: 70px;
  opacity: 0;
}

.upload-img > a:hover {
  background-color: #464646;
}

.upload-img > a:active {
  background-color: #111111;
}

</style>