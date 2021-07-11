<template>
  <view class="cover-img" :style="{ height: windowHeight + 'px' }">
    <camera
      mode="normal"
      device-position="back"
      flash="off"
      :style="{ height: cameraHeight + 'px' }"
    >
      <cover-view class="controls" style="width: 100%; height: 100%">
        <!-- 头像面 -->
        <cover-image
          v-if="idcardFrontSide === 'front'"
          class="icon-w569-h828"
          src="https://img-blog.csdnimg.cn/20210126144225906.png"
        />
        <!-- 国徽面 -->
        <cover-image
          v-else
          class="icon-w569-h828"
          src="https://img-blog.csdnimg.cn/20210126144317636.png"
        />
      </cover-view>
    </camera>
    <view class="bottom font-36-fff">
      <view class="wrap">
        <view class="back" @click="back">取消</view>
        <view @click="takePhoto" class="photograph">
          <image
            class="icon-w131-h131"
            src="/static/take_camera_btn_icon.png"
            mode="heightFix"
          >
          </image>
        </view>
        <view @click="chooseImage" class="album"> 相册 </view>
      </view>
    </view>
    <view class="creatImg" v-if="show">
      <image class="cardImg" :src="img" mode="heightFix"></image>
    </view>
  </view>
</template>

<script>
var that;
export default {
  data() {
    return {
      cameraContext: {},
      windowHeight: "",
      cameraHeight: 0,
      img: "",
      show: false,
    };
  },
  onLoad(options) {
    that = this;
    if (uni.createCameraContext) {
      that.cameraContext = uni.createCameraContext();
    } else {
      // 如果希望用户在最新版本的客户端上体验您的小程序，可以这样子提示
      uni.showModal({
        title: "提示",
        content:
          "当前微信版本过低，无法使用该功能，请升级到最新微信版本后重试。",
      });
    }
  },
  onShow() {
    let systemInfo = uni.getSystemInfoSync();
    that.windowHeight = systemInfo.windowHeight;
    that.cameraHeight = systemInfo.windowHeight - 80;
  },
  methods: {
    // 拍照
    takePhoto() {
      uni.showLoading({
        title: "上传中，请稍候",
      });
      that.cameraContext.takePhoto({
        quality: "normal",
        success: (res) => {
          console.log(res);
          that.img = res.tempImagePath;
          console.log(that.img);
          that.show = true;
          uni.redirectTo({
            url: "/pages/photo/test",
            animationType: "pop-in",
            animationDuration: 200,
          });
          uni.hideLoading();
        },
        fail: (err) => {
          uni.showToast({
            title: "拍照失败，请检查系统是否授权",
            icon: "none",
            duration: 1200,
          });
        },
      });
    },
    // 从相册选取
    chooseImage() {
      uni.chooseImage({
        count: 1,
        sizeType: ["original", "compressed"],
        sourceType: ["album"],
        success: (res) => {
          console.log(res);
          that.img = res.tempFilePaths[0];
          that.show = true;
        },
        fail: (err) => {},
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.cover-img {
  .icon-w569-h828 {
    width: 569rpx;
    height: 828rpx;
  }
  .controls {
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .bottom {
    width: 100%;
    position: fixed;
    bottom: 0;
    background-color: #000;
    .wrap {
      display: flex;
      align-items: center;
      justify-content: space-between;
      height: 160rpx;
      width: 100%;
      view {
        color: #fff;
      }
      .back {
        width: 150rpx;
        text-align: center;
      }
      .album {
        width: 150rpx;
        text-align: center;
      }
      .photograph {
        height: 100rpx;
        image {
          height: 100rpx;
        }
      }
    }
  }
  .creatImg {
    position: absolute;
    top: 0;
    width: 100%;
    height: 100%;
    .cardImg {
      height: 100%;
    }
  }
}
</style>
