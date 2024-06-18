<script>
import axios from "axios";
import Swal from "sweetalert2";
export default {
  data() {
    return {
      feedbackList: [],       // 儲存所有回饋的訊息
      selectedFeedback: null, // 儲存選中的回饋訊息 預設null
      feedbackHeight: "0px",  // 儲存詳細資料的高度
      searchResults: "",      //搜尋結果
    };
  },
  created() {
    this.getFeedback();
  },
  methods: {
    //取得所有意見回饋資料
    async getFeedback() {
      try {
        const response = await axios.get("http://localhost:8080/feedBack/getAllFeedBackInfo");
        this.feedbackList = response.data.feedbackList;
        console.log(this.feedbackList);
      } catch (error) {
        console.error(error);
      }
    },
    // 模糊搜尋使用者名稱
    async getUserName() {
      try {
        const response = await axios.get(`http://localhost:8080/feedBack/getUserName?name=${this.searchResults}`);
        const { rtnCode, feedbackList } = response.data;
        console.log({ rtnCode, feedbackList });
        if (rtnCode === "PARAM_ERROR") {
          // 如果回傳 rtnCode 為 PARAM_ERROR，顯示警告訊息
          Swal.fire({
            title: "搜尋失敗!!",
            text: "沒有該對應的使用者資料，請確認輸入的搜尋條件!!",
            icon: "error",
            confirmButtonText: "OK",
          });
        } else if (rtnCode === "SUCCESSFUL") {
          // 如果回傳 rtnCode 為 SUCCESSFUL，更新 feedbackList
          this.feedbackList = feedbackList;
        }
      } catch (error) {
        console.error(error);
      }
    },
    //渲染內容高度
    showDetails(index) {
      if (this.selectedFeedback === this.feedbackList[index]) {
        this.selectedFeedback = null;
        this.feedbackHeight = "0px"; // 收回時將高度設為 0
      } else if (this.selectedFeedback !== null) {
        const prevIndex = this.feedbackList.indexOf(this.selectedFeedback);
        this.selectedFeedback = this.feedbackList[index];
        this.feedbackHeight = `${
          document.querySelectorAll(".details")[prevIndex].scrollHeight
        }px`;
      } else {
        this.selectedFeedback = this.feedbackList[index];
        const detailsElement = document.querySelectorAll(".details")[index];
        this.feedbackHeight = `${detailsElement.scrollHeight}px`; // 展開時根據內容高度設定
      }
    },
  },
};
</script>

<template>
  <div class="ALL">
    <div class="feedback-list">
      <h2 style="text-align: center">★用戶意見與回饋★</h2>
      <div class="search">
        <input
          v-model="searchResults"
          placeholder="請輸入姓名做搜尋"
          class="searchInput"/>
        <button @click="getUserName" class="searchIcon">
          <i class="fa-solid fa-magnifying-glass"></i>
        </button>
      </div>
      <br />
      <ul class="feedback-items">
        <li
          v-for="(feedback, index) in feedbackList"
          :key="index"
          class="feedback-item"
        >
          <div @click="showDetails(index)" class="feedback-title">
            <span>● 用戶意見與回饋 {{ index + 1 }}:</span>
            <span style="flex: 1; text-align: left">{{ feedback.name }}</span>
            <div class="arrow-icon">
              {{ selectedFeedback === feedback ? "▲" : "▼" }}
            </div>
          </div>
          <div
            class="details"
            :style="{
              maxHeight: selectedFeedback === feedback ? feedbackHeight : '0px',
            }"
          >
            <h3>詳細資料 :</h3>
            <b>姓名:</b> {{ feedback.name }}<br />
            <b>電話:</b> {{ feedback.phone }}<br />
            <b>Email:</b> {{ feedback.email }}<br />
            <b>意見與回饋:</b> {{ feedback.feedback }}<br />
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.ALL {
  width: 100vw;
  height: 100vh;
  background-color: rgb(246, 246, 246);
}
.feedback-list {
  margin: 20px;
  height: 100vh;
  overflow-y: auto; /* 如果内容过多出现滚动条 */

  .search {
    height: 35px;
    display: flex;
    justify-content: center;
    .searchInput {
      width: 50vw;
      border-radius: 15px;
      position: relative;
    }
    .searchIcon {
      width: 5vw;
      border-radius: 15px;
    }
  }
}

.feedback-items {
  list-style: none;
  padding: 0;
}

.feedback-item {
  margin-bottom: 10px; /* 調整條列間距 */
  padding: 10px;
  border: 1px solid #ccc;
  cursor: pointer;
}

.details {
  overflow: hidden;
  max-height: 0;
  transition: max-height 0.5s ease-out; /* 過渡效果 */
}

.show-details {
  max-height: 500px; /* 調整過場動畫的最大高度 */
}

.feedback-title {
  display: flex; /* 讓資料居左並對齊 */
  justify-content: space-between; /* 讓箭頭與資料之間有空間 */
  align-items: center; /* 垂直置中箭頭 */
}

.arrow-icon {
  font-size: 20px; /* 調整箭頭大小 */
}
</style>

