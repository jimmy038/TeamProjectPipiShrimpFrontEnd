<script>
import axios from "axios";
import Swal from "sweetalert2";
export default {
    data() {
    return {
      email: "",
    };
    },
    methods: {
      //忘記密碼方法
      forgotPwd(){
        if(!this.email.trim()){
          Swal.fire({
            title: "發送失敗!!",
            text:"電子郵件欄位不得為空!!",
            icon: "error",
            confirmButtonText: "OK",
          });
          return;
        }
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        const isValidEmail = emailRegex.test(this.email);
        if (!isValidEmail) {
          Swal.fire({
            title: "發送失敗!!",
            text:"請輸入有效的電子郵件!!",
            icon: "error",
            confirmButtonText: "OK",
          });
          return;
        }
        const url = `http://localhost:8080/user/sentForgotPwd?email=${this.email}`;
        axios.get(url).then((response) => {
          console.log(response.data);
          const responseData = response.data;
          console.log(responseData.rtnCode);
          if(responseData.rtnCode === "EMAIL_NOT_FOUND"){
            Swal.fire({
            title: "電子郵件錯誤!!",
            text: "電子郵件輸入錯誤，或此電子郵件尚未註冊!!",
            icon: "error",
            confirmButtonText: "OK",
          });
          return;
          }else{
          console.log("請求成功", response.data);
          this.showAlert();
          this.$router.push("/UserPage/loginPage");
          }
        })
        .catch((error) => {
          console.error('請求失敗', error);
        })
      },
      //套件sweetalert2，忘記密碼
      showAlert() {
    Swal.fire({
        title: "已發送一次性密碼至您的電子郵件!!",
        text: "請至email查看，再使用您的一次性密碼做登入後更改密碼，謝謝!!",
        icon: "success",
        confirmButtonText: "OK",
        });
      },
    },
};
</script>

<template>
    <div class="secondtitle2">
        <RouterLink class="backbtn" to="/UserPage/loginPage">回首頁</RouterLink>
    </div>
    <div class="mainLoginShow">
    <div class="leftShow">
        <h1>
            <i class="fa-solid fa-shrimp"><b> 呱皮皮蝦</b> </i>
        </h1>
    </div>
    <div class="login-box">
        <div class="lb-header">
            <h2>歡迎回來</h2>
        </div>
        <br />
        <div class="change-password-container">
        <h1>找回密碼</h1><br><br>
        <form @submit.prevent="submitForm" class="change-password-form">
          <div class="inputList">
            <label for="email"> 信箱 :</label>
            <input type="email" v-model="email" required class="input-field" placeholder="Your-email" />
          </div><br><br>
          <button class="btn" type="submit" @click="forgotPwd">確定</button>
        </form>
      </div>
    </div>
  </div>
</template>


<style lang="scss" scoped>
.secondtitle2 {
  justify-content: space-between;
  display: flex;

  border: 0px solid rgb(255, 0, 0);
  width: 100vw;
  background-color: rgb(246, 246, 246);
  height: 10vh;
  align-items: center;
  .backbtn {
    font-size: 16pt;
    margin: 10px;
    border-radius: 10px;
    padding: 10px;
    width: 10vw;
    justify-content: center;
    display: flex;
    align-items: center;

    background-color: rgb(223, 223, 223);
  }
  a {
    border-radius: 10px;
    padding: 5px;
    transition: all 0.5s ease;
    text-decoration: none;
    color: black;

    &:hover {
      color: red;
      background-color: rgba(118, 118, 117, 0.5);
    }
  }
}
.mainLoginShow {
  border: 0px solid red;
  height: 80vh;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100vw;
  background-color: rgb(246, 246, 246);
  .leftShow {
    background-color: rgb(0, 0, 0);
    height: 70vh;
    width: 30vw;
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
  }

  .login-box {
    position: relative;
    margin: 10px;
    width: 700px;
    height: 70vh;
    background-color: #fff;
    padding: 10px;
    border-radius: 3px;
    box-shadow: 0px 2px 3px 0px rgba(0, 0, 0, 0.33);
    .inputList {
      border: 0px solid rgb(255, 0, 0);
      width: 30vw;
      display: flex;
      justify-content: space-between;
      align-items: center;
      position: relative;
      left: 3%;
      .sendBtn {
        height: 5vh;
        width: 18%;
      }
    }

    .inputList1 {
      border: 0px solid rgb(255, 0, 0);
      width: 30vw;
      display: flex;
      align-items: center;
      .input-field {
        width: 21vw;
      }
    }
    .change-password-container {
      max-width: 600px;

      border: 0px solid rgb(255, 0, 0);
      height: 57vh;

      margin: 0 auto;
      padding: 20px;
      text-align: center;
    }

    .change-password-form {
      display: flex;
      flex-direction: column;
      align-items: center;
    }

    .label {
      margin-bottom: 5px;
      text-align: left;
    }

    .input-field {
      width: 70%;
      padding: 10px;
      margin: 5px 0 15px 0;
      box-sizing: border-box;
      margin-right: 15%;
    }

    .btn {
      background-color: rgb(76, 155, 175);
      color: white;
      padding: 10px 15px;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      width: 50%;
    }

    .btn:hover {
      background-color: rgb(27, 191, 197);
    }
  }
}
</style>
