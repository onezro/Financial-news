<template>
  <div class="singles-search">
    <div class="fixed">
      <div class="header">
        <span @click="goBack">&lt;</span><span>全部单行本</span>
      </div>
      <div class="input" @keydown.enter="keyDown">
        <i><img src="../../assets/search.svg" /></i>
        <input
          v-model="val"
          class="search-bar"
          placeholder="搜索"
          type="text"
        />
        <i @click="deleteVal" v-show="val"
          ><img src="../../assets/img/0K.png"
        /></i>
      </div>
    </div>

    <div class="list-container" v-show="val">
      <div class="list">
        <div
          v-for="(item, index) in list"
          :key="index"
          class="list-item"
          @click="goMagazineData(item.content.type, item.content.id)"
        >
          <i class="subject-icon"><img :src="item.content.cover_url" /></i>
          <div class="text">
            <span>{{ item.content.name }}</span>
          </div>
        </div>

        <div class="empty" v-show="!list.length">
          <i><img src="../../assets/img/3T.png" /></i>
          <div>暂无搜索结果，换个关键字试试吧</div>
        </div>
      </div>
    </div>

    <div class="list-container" v-show="!val">
      <div class="list">
        <div
          v-for="(item, index) in singles"
          :key="index"
          class="list-item"
          @click="goMagazineData(item.type, item.id)"
        >
          <i class="subject-icon"><img :src="item.cover_url" /></i>
          <div class="text">
            <span>{{ item.name }}</span>
            <span>{{ item.articles_count }}篇文章</span>
          </div>
          <div v-show="item.price" class="price">
            {{ item.price }}
          </div>
        </div>
      </div>

      <wd-infinite-load
        finished-text
        ref="loadmore"
        @loadmore="loadmore"
        :loading="loading"
      ></wd-infinite-load>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      singles: [],
      list: [],
      val: null,
      num: 10,
      loading: false,
      time: 3,
    };
  },
  created() {
    this.getSinglesDate();
  },
  methods: {
    goMagazineData(type, id) {
      type = type.slice(0, 1).toUpperCase() + type.slice(1).toLowerCase();
      this.$router.push(
        `/magazineData?&magazineData_type=${type}&magazineData_id=${id}`
      );
    },
    keyDown() {
      this.$axios
        .get(
          `https://api2021.cbnweek.com/v4/pg_search_documents?page=1&per=${this.num}&query=${this.val}&query_type=subject&type=android`
        )
        .then(({ data }) => {
          this.list = data;
          console.log(this.list);
        });
    },
    deleteVal() {
      this.val = null;
    },
    goBack() {
      this.$router.go(-1);
    },
    getSinglesDate() {
      this.$axios
        .get(
          `http://api2021.cbnweek.com:80/v4/theme_subjects?page=1&per=${this.num}`
        )
        .then(({ data }) => {
          this.singles = data;
          console.log(data);
        });
    },
    loadmore() {
      this.loading = true;

      if (this.time) {
        this.num += 10;
        setTimeout(() => {
          this.$axios
            .get(
              `http://api2021.cbnweek.com:80/v4/theme_subjects?page=1&per=${this.num}`
            )
            .then(({ data }) => {
              this.singles = data;
              this.loading = false;
              this.time--;
            });
        }, 1000);
      } else {
        this.$refs.loadmore.loadEnd();
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.singles-search {
  width: 100vw;
  .fixed {
    position: fixed;
    z-index: 999;
    background-color: white;
    top: 0;
    left: 0;
    height: 12vh;
    width: 100vw;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    .header {
      margin-left: 2vw;
      :nth-child(1) {
        font-size: 30px;
      }
      :nth-child(2) {
        display: inline-block;
        font-size: 26px;
        margin-left: 25vw;
      }
    }
    .input {
      margin-left: 2vw;
      width: 96vw;
      height: 4vh;
      background-color: rgba(119, 119, 119, 0.2);
      border-radius: 10vw;
      display: flex;
      i {
        margin-left: 4vw;
        margin-top: 1vh;
        margin-right: 1vw;
        img {
          width: 5vw;
          height: 5vw;
        }
      }
      .search-bar {
        outline: 0;
        border: 0;
        width: 75%;
        background-color: rgba(119, 119, 119, 0);
      }
    }
  }

  .list-container {
    margin-top: 12.5vh;
    margin-left: 2vw;
    .list {
      .list-item {
        display: flex;
        margin: 1.5vh 0;
        position: relative;

        .subject-icon {
          width: 20vw;
          height: 25vw;
          margin-right: 2vw;
          img {
            width: 20vw;
            height: 25vw;
          }
        }
        .text {
          display: flex;
          flex-direction: column;
          :nth-child(1) {
            font-size: 18px;
            font-weight: 700;
          }
          :nth-child(2) {
            font-size: 15px;
            margin-top: 1vh;
            color: rgba(119, 119, 119, 0.6);
          }
        }
        .price {
          width: 8vw;
          height: 2vh;
          position: absolute;
          left: 0;
          top: 0.5vh;
          background-color: #c9ab79;
          z-index: 100;
          border-radius: 0 20px 20px 0;
          text-align: center;
          color: white;
          line-height: 2.2vh;
          font-size: 12px;
        }
      }

      .empty {
        width: 100vw;
        display: flex;
        flex-direction: column;
        i {
          margin-left: 28vw;
          img {
            height: 40vw;
            width: 40vw;
          }
        }
        div {
          margin-left: 20vw;
        }
      }
    }
  }
}
</style>