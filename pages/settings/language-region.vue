<template>
  <view class="language-region-container">
    <!-- Header -->
    <view class="header">
      <view class="back-button" @click="navigateBack">
        <image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
      </view>
      <text class="header-title">语言和地区</text>
      <view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
    </view>
    
    <!-- Content -->
    <scroll-view scroll-y class="settings-content">
      <!-- Language Settings -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">应用语言</text>
        </view>
        
        <radio-group @change="onLanguageChange">
          <view class="option-item" v-for="(lang, index) in languages" :key="lang.code">
            <view class="option-info">
              <text class="option-name">{{lang.name}}</text>
              <text class="option-desc" v-if="lang.nativeName && lang.nativeName !== lang.name">{{lang.nativeName}}</text>
            </view>
            <radio :value="lang.code" :checked="currentLanguage === lang.code" color="#2196f3" />
          </view>
        </radio-group>
      </view>
      
      <!-- Region Settings -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">内容地区</text>
        </view>
        
        <view class="region-selector">
          <picker @change="onRegionChange" :value="regionIndex" :range="regions" range-key="name">
            <view class="picker-view">
              <text>{{ regions[regionIndex].name }}</text>
              <image src="/static/icons/arrow-down.png" mode="aspectFit" class="arrow-icon"></image>
            </view>
          </picker>
        </view>
        
        <view class="region-note">
          <text>地区设置将影响应用内推荐内容和文化相关特性</text>
        </view>
      </view>
      
      <!-- Date & Time Format -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">日期和时间格式</text>
        </view>
        
        <view class="option-item">
          <text class="option-name">24小时制</text>
          <switch :checked="is24HourFormat" @change="toggle24HourFormat" color="#2196f3" />
        </view>
        
        <view class="date-format-selector">
          <view class="format-option">
            <text class="format-label">日期格式</text>
            <picker @change="onDateFormatChange" :value="dateFormatIndex" :range="dateFormats" range-key="display">
              <view class="format-picker">
                <text>{{ dateFormats[dateFormatIndex].display }}</text>
                <image src="/static/icons/arrow-down.png" mode="aspectFit" class="arrow-icon"></image>
              </view>
            </picker>
          </view>
          
          <view class="format-preview">
            <text>预览: {{ getFormattedDate() }}</text>
          </view>
        </view>
      </view>
      
      <!-- Keyboard Language Settings (Mobile Only) -->
      <view class="section" v-if="!isPC">
        <view class="section-header">
          <text class="section-title">键盘语言</text>
        </view>
        
        <view class="keyboard-note">
          <text>键盘语言设置需要在设备系统设置中修改</text>
        </view>
        
        <button class="open-settings-btn" @click="openSystemSettings">打开系统设置</button>
      </view>
      
      <button class="save-button" @click="saveSettings">保存设置</button>
    </scroll-view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      isPC: false,
      currentLanguage: 'zh-CN',
      languages: [
        { code: 'zh-CN', name: '简体中文', nativeName: '简体中文' },
        { code: 'zh-TW', name: '繁体中文', nativeName: '繁體中文' },
        { code: 'en-US', name: '英语', nativeName: 'English' },
        { code: 'ja-JP', name: '日语', nativeName: '日本語' },
        { code: 'ko-KR', name: '韩语', nativeName: '한국어' }
      ],
      regionIndex: 0,
      regions: [
        { code: 'CN', name: '中国大陆' },
        { code: 'HK', name: '中国香港' },
        { code: 'TW', name: '中国台湾' },
        { code: 'US', name: '美国' },
        { code: 'JP', name: '日本' },
        { code: 'KR', name: '韩国' },
        { code: 'SG', name: '新加坡' }
      ],
      is24HourFormat: true,
      dateFormatIndex: 0,
      dateFormats: [
        { value: 'yyyy-MM-dd', display: '年-月-日 (2025-04-22)' },
        { value: 'dd/MM/yyyy', display: '日/月/年 (22/04/2025)' },
        { value: 'MM/dd/yyyy', display: '月/日/年 (04/22/2025)' }
      ]
    }
  },
  onLoad() {
    // Check if running on PC
    try {
      const info = uni.getSystemInfoSync();
      this.isPC = info.platform === 'windows' || info.platform === 'mac';
    } catch (e) {
      console.error('Failed to get system info:', e);
    }
    
    // Load saved settings if available
    const savedSettings = uni.getStorageSync('languageSettings');
    if (savedSettings) {
      try {
        const settings = JSON.parse(savedSettings);
        this.currentLanguage = settings.language || 'zh-CN';
        
        // Set region index
        if (settings.regionCode) {
          const index = this.regions.findIndex(r => r.code === settings.regionCode);
          this.regionIndex = index >= 0 ? index : 0;
        }
        
        this.is24HourFormat = settings.is24HourFormat !== undefined ? settings.is24HourFormat : true;
        
        // Set date format index
        if (settings.dateFormat) {
          const index = this.dateFormats.findIndex(f => f.value === settings.dateFormat);
          this.dateFormatIndex = index >= 0 ? index : 0;
        }
      } catch (e) {
        console.error('Error parsing saved language settings:', e);
      }
    }
  },
  methods: {
    navigateBack() {
      uni.navigateBack();
    },
    onLanguageChange(e) {
      this.currentLanguage = e.detail.value;
    },
    onRegionChange(e) {
      this.regionIndex = e.detail.value;
    },
    toggle24HourFormat(e) {
      this.is24HourFormat = e.detail.value;
    },
    onDateFormatChange(e) {
      this.dateFormatIndex = e.detail.value;
    },
    getFormattedDate() {
      const now = new Date();
      const format = this.dateFormats[this.dateFormatIndex].value;
      
      // Simple formatter for demo purposes
      const year = now.getFullYear();
      const month = String(now.getMonth() + 1).padStart(2, '0');
      const day = String(now.getDate()).padStart(2, '0');
      
      let formattedDate = format;
      formattedDate = formattedDate.replace('yyyy', year);
      formattedDate = formattedDate.replace('MM', month);
      formattedDate = formattedDate.replace('dd', day);
      
      return formattedDate;
    },
    openSystemSettings() {
      // This is just a placeholder since direct access to system settings
      // is limited by platform capabilities
      uni.showToast({
        title: '请前往设备系统设置中修改键盘语言',
        icon: 'none',
        duration: 3000
      });
    },
    saveSettings() {
      const settings = {
        language: this.currentLanguage,
        regionCode: this.regions[this.regionIndex].code,
        is24HourFormat: this.is24HourFormat,
        dateFormat: this.dateFormats[this.dateFormatIndex].value
      };
      
      uni.setStorageSync('languageSettings', JSON.stringify(settings));
      
      // Show confirmation and go back
      uni.showToast({
        title: '设置已保存',
        icon: 'success',
        duration: 2000,
        success: () => {
          setTimeout(() => {
            uni.navigateBack();
          }, 2000);
        }
      });
    }
  }
}
</script>

<style>
.language-region-container {
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

.settings-content {
  flex: 1;
  padding: 30rpx;
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

.option-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 25rpx 30rpx;
  border-bottom: 1rpx solid #f0f0f0;
}

.option-item:last-child {
  border-bottom: none;
}

.option-info {
  display: flex;
  flex-direction: column;
}

.option-name {
  font-size: 28rpx;
  color: #333;
}

.option-desc {
  font-size: 24rpx;
  color: #999;
  margin-top: 5rpx;
}

.region-selector {
  padding: 25rpx 30rpx;
}

.picker-view {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 80rpx;
  border: 1rpx solid #ddd;
  border-radius: 10rpx;
  padding: 0 20rpx;
}

.arrow-icon {
  width: 32rpx;
  height: 32rpx;
}

.region-note {
  padding: 0 30rpx 25rpx;
  font-size: 24rpx;
  color: #999;
}

.date-format-selector {
  padding: 25rpx 30rpx;
}

.format-option {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20rpx;
}

.format-label {
  font-size: 28rpx;
  color: #333;
}

.format-picker {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 450rpx;
  height: 70rpx;
  border: 1rpx solid #ddd;
  border-radius: 10rpx;
  padding: 0 20rpx;
}

.format-preview {
  font-size: 24rpx;
  color: #666;
  margin-top: 15rpx;
}

.keyboard-note {
  padding: 25rpx 30rpx;
  font-size: 28rpx;
  color: #666;
}

.open-settings-btn {
  margin: 0 30rpx 30rpx;
  height: 80rpx;
  line-height: 80rpx;
  background-color: #f5f5f5;
  color: #2196f3;
  font-size: 28rpx;
  border: 1rpx solid #2196f3;
  border-radius: 10rpx;
}

.save-button {
  margin: 30rpx 0 100rpx;
  height: 90rpx;
  line-height: 90rpx;
  background-color: #2196f3;
  color: white;
  font-size: 32rpx;
  border-radius: 45rpx;
}
</style> 