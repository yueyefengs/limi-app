<template>
  <view class="about-container">
    <!-- Header -->
    <view class="header">
      <view class="back-button" @click="navigateBack">
        <image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
      </view>
      <text class="header-title">关于小智</text>
      <view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
    </view>
    
    <!-- Content -->
    <scroll-view scroll-y class="about-content">
      <!-- Logo and App Info -->
      <view class="app-info">
        <image src="/static/images/logo.png" mode="aspectFit" class="app-logo"></image>
        <text class="app-name">小智伴学</text>
        <text class="app-version">版本 {{appVersion}}</text>
        <text class="app-build">构建号: {{buildNumber}}</text>
      </view>
      
      <!-- Version Information -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">版本信息</text>
        </view>
        
        <view class="info-item">
          <text class="info-label">应用版本</text>
          <text class="info-value">{{appVersion}}</text>
        </view>
        
        <view class="info-item">
          <text class="info-label">设备系统</text>
          <text class="info-value">{{systemInfo.platform}} {{systemInfo.system}}</text>
        </view>
        
        <view class="info-item">
          <text class="info-label">运行环境</text>
          <text class="info-value">{{systemInfo.SDKVersion}}</text>
        </view>
        
        <view class="update-button" @click="checkForUpdates">
          <text>检查更新</text>
        </view>
      </view>
      
      <!-- Legal Information -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">法律信息</text>
        </view>
        
        <view class="legal-item" @click="navigateTo('/pages/settings/user-agreement')">
          <text class="legal-text">使用条款</text>
          <image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
        </view>
        
        <view class="legal-item" @click="navigateTo('/pages/settings/privacy-policy')">
          <text class="legal-text">隐私政策</text>
          <image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
        </view>
        
        <view class="legal-item" @click="showLicenses">
          <text class="legal-text">开源许可</text>
          <image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
        </view>
      </view>
      
      <!-- Contact Information -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">联系我们</text>
        </view>
        
        <view class="contact-item">
          <text class="contact-label">客服电话</text>
          <text class="contact-value">400-888-9999</text>
        </view>
        
        <view class="contact-item">
          <text class="contact-label">客服邮箱</text>
          <text class="contact-value">support@xiaozhi.ai</text>
        </view>
        
        <view class="contact-item">
          <text class="contact-label">官方网站</text>
          <text class="contact-value">www.xiaozhi.ai</text>
        </view>
      </view>
      
      <view class="copyright">
        <text>© 2025 小智智能科技有限公司</text>
        <text>保留所有权利</text>
      </view>
    </scroll-view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      appVersion: 'v1.0.3',
      buildNumber: '2025042201',
      systemInfo: {
        platform: '',
        system: '',
        SDKVersion: ''
      }
    }
  },
  onLoad() {
    // Get system information
    try {
      const info = uni.getSystemInfoSync();
      this.systemInfo = {
        platform: info.platform,
        system: info.system,
        SDKVersion: info.SDKVersion
      };
    } catch (e) {
      console.error('Failed to get system info:', e);
    }
  },
  methods: {
    navigateBack() {
      uni.navigateBack();
    },
    navigateTo(url) {
      uni.navigateTo({
        url: url,
        fail: () => {
          uni.showToast({
            title: '页面开发中，即将上线',
            icon: 'none'
          });
        }
      });
    },
    checkForUpdates() {
      uni.showLoading({
        title: '检查更新中...'
      });
      
      // 模拟检查更新
      setTimeout(() => {
        uni.hideLoading();
        
        // 随机决定是否有新版本
        const hasNewVersion = Math.random() > 0.7;
        
        if (hasNewVersion) {
          uni.showModal({
            title: '发现新版本',
            content: '新版本v1.0.4已发布，是否立即更新？',
            cancelText: '稍后',
            confirmText: '立即更新',
            success: (res) => {
              if (res.confirm) {
                this.startUpdate();
              }
            }
          });
        } else {
          uni.showModal({
            title: '检查结果',
            content: '您当前使用的已是最新版本',
            showCancel: false,
            confirmText: '确定'
          });
        }
      }, 1500);
    },
    startUpdate() {
      uni.showLoading({
        title: '准备更新...'
      });
      
      // 模拟更新过程
      setTimeout(() => {
        uni.hideLoading();
        
        uni.showModal({
          title: '下载完成',
          content: '新版本已下载完成，重启应用后生效',
          cancelText: '稍后重启',
          confirmText: '立即重启',
          success: (res) => {
            if (res.confirm) {
              // 模拟重启应用
              uni.showLoading({
                title: '正在重启...'
              });
              
              setTimeout(() => {
                uni.hideLoading();
                uni.reLaunch({
                  url: '/pages/index/index'
                });
              }, 1500);
            }
          }
        });
      }, 2000);
    },
    showLicenses() {
      uni.showModal({
        title: '开源许可',
        content: '小智应用使用了以下开源项目：\n\n- Vue.js (MIT许可)\n- uni-app (Apache 2.0许可)\n- axios (MIT许可)\n- lottie-web (MIT许可)\n\n完整许可信息请访问官方网站。',
        showCancel: false,
        confirmText: '确定'
      });
    }
  }
}
</script>

<style>
.about-container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  background-color: #f5f5f5;
}

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 20rpx 30rpx;
  background-color: #ffffff;
  box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
}

.back-button {
  width: 50rpx;
  height: 50rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.back-icon {
  width: 32rpx;
  height: 32rpx;
}

.header-title {
  font-size: 36rpx;
  font-weight: bold;
  color: #333;
}

.about-content {
  flex: 1;
  padding: 30rpx;
}

.app-info {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 40rpx 0;
}

.app-logo {
  width: 150rpx;
  height: 150rpx;
  margin-bottom: 20rpx;
}

.app-name {
  font-size: 40rpx;
  font-weight: bold;
  color: #333;
  margin-bottom: 10rpx;
}

.app-version {
  font-size: 28rpx;
  color: #666;
  margin-bottom: 5rpx;
}

.app-build {
  font-size: 24rpx;
  color: #999;
}

.section {
  background-color: #ffffff;
  border-radius: 20rpx;
  margin-bottom: 30rpx;
  overflow: hidden;
  box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.03);
}

.section-header {
  padding: 20rpx 30rpx;
  border-bottom: 1rpx solid #f0f0f0;
}

.section-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
}

.info-item, .contact-item {
  display: flex;
  justify-content: space-between;
  padding: 25rpx 30rpx;
  border-bottom: 1rpx solid #f0f0f0;
}

.info-item:last-child, .contact-item:last-child {
  border-bottom: none;
}

.info-label, .contact-label {
  font-size: 28rpx;
  color: #666;
}

.info-value, .contact-value {
  font-size: 28rpx;
  color: #333;
}

.update-button {
  padding: 25rpx 30rpx;
  text-align: center;
  border-top: 1rpx solid #f0f0f0;
}

.update-button text {
  font-size: 28rpx;
  color: #2196f3;
}

.legal-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 25rpx 30rpx;
  border-bottom: 1rpx solid #f0f0f0;
}

.legal-item:last-child {
  border-bottom: none;
}

.legal-text {
  font-size: 28rpx;
  color: #333;
}

.arrow-icon {
  width: 32rpx;
  height: 32rpx;
}

.copyright {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 40rpx 0 100rpx;
}

.copyright text {
  font-size: 24rpx;
  color: #999;
  line-height: 1.5;
}
</style> 