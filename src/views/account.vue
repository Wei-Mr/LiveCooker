<template>
  <div class="account">
    <van-nav-bar title="账号管理" left-text="返回" left-arrow :fixed="true" @click-left="back" />

    <van-cell-group>
      <van-cell class="avatar" title="头像" size="large">
        <template #right-icon>
          <div class="person-img">
            <img class="auto-img" :src="userInfo.userImg" alt />
            <van-uploader class="v-uploader" :after-read="upload" />
          </div>
        </template>
      </van-cell>
      <van-cell title="用户id" size="large" :value="userInfo.userId" />
      <van-cell title="手机号" size="large" :value="userInfo.phone" />
      <van-cell class="avatar" title="昵称" size="large">
        <template #right-icon>
          <div>
            <van-field
              class="v-field"
              input-align="right"
              v-model="userInfo.nickName"
              @change="changeUserInfo('http://www.kangliuyong.com:10002/updateNickName', 'nickName', userInfo.nickName)"
            />
          </div>
        </template>
      </van-cell>
      <van-cell title="简介" size="large">
        <template #right-icon>
          <div>
            <van-field
              class="v-textarea"
              type="textarea"
              input-align="right"
              rows="2"
              autosize
              maxlength="30"
              v-model="userInfo.desc"
              @change="changeUserInfo('http://www.kangliuyong.com:10002/updateDesc', 'desc', userInfo.desc)"
            />
          </div>
        </template>
      </van-cell>
    </van-cell-group>
  </div>
</template>

<script>
import { utils } from "../utils/utils";

export default {
  name: "Account",

  data() {
    return {
      userInfo: {
        userImg: "",
        phone: "",
        userId: "",
        nickName: "",
        desc: ""
      }
    };
  },

  created() {
    this.$store.state.vanTabbar = false;
    this.getUserInfo();
  },
  destroyed() {
    // 当组件被销毁时候，修改导航数据
    this.$store.state.vanTabbar = true;
  },

  methods: {
    back() {
      this.$router.go(-1);
    },

    //获取用户信息
    getUserInfo() {
      let tokenString = localStorage.getItem("_t");

      //加载提示
      this.$toast.loading({
        forbidClick: true,
        duration: 0,
        message: "加载中..."
      });

      this.axios({
        method: "GET",
        url: "http://www.kangliuyong.com:10002/findAccountInfo",
        params: {
          appkey:'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
          tokenString
        }
      })
        .then(result => {
          this.$toast.clear();

          this.userInfo = result.data.result[0];
        })
        .catch(err => {
          this.$toast.clear();
          
        });
    },

    //修改昵称、简介
    changeUserInfo(url, key, value) {
      let tokenString = localStorage.getItem("_t");

      //加载提示
      this.$toast.loading({
        forbidClick: true,
        duration: 0,
        message: "加载中..."
      });

      this.axios({
        method: "POST",
        url,
        data: {
          appkey:'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
          tokenString,
          [key]: value
        },
        transformRequest: utils.transformRequest
      })
        .then(result => {
          this.$toast.clear();
          
        })
        .catch(err => {
          this.$toast.clear();
          
        });
    },

    //上传头像
    upload(file) {
      // 

      //控制上传图片大小不能超过 300KB
      let imgSize = 300 * 1024;
      if (file.file.size > imgSize) {
        this.$toast("上传图片大小不能超过300KB");
        return;
      }

      //将图片上传到服务器

      //获取图片类型
      let imgType = file.file.type.split("/")[1];
      // 

      //处理图片的base64
      let serverBase64Img = file.content.replace(
        /data:image\/[A-Za-z]+;base64,/,
        ""
      );
      // 

      let tokenString = localStorage.getItem("_t");

      //加载提示
      this.$toast.loading({
        forbidClick: true,
        duration: 0,
        message: "加载中..."
      });

      this.axios({
        method: "POST",
        url: "http://www.kangliuyong.com:10002/updateAvatar",
        data: {
          appkey:'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
          tokenString,
          imgType,
          serverBase64Img
        },
        transformRequest: utils.transformRequest
      })
        .then(result => {
          this.$toast.clear();
          // 
          this.userInfo.userImg = result.data.userImg;
        })
        .catch(err => {
          this.$toast.clear();
          
        });
    }
  }
};
</script>

<style lang="less" scoped>
.account {
  padding-top: 46px;
  .avatar {
    padding-top: 0;
    padding-bottom: 0;
    line-height: 48px;
    .person-img {
      width: 40px;
      margin-top: 4px;
      position: relative;
    }
    .v-uploader {
      position: absolute;
      width: 40px;
      height: 40px;
      left: 0;
      top: 0;
      overflow: hidden;
      z-index: 2;
      opacity: 0;
    }
  }
  /deep/ .v-field {
    padding: 0;
    line-height: 48px;
  }

  /deep/ .v-textarea {
    padding: 0;
  }

  /deep/ .van-field__control {
    color: #9697a6;
  }
}
</style>