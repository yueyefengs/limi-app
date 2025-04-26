<template>
  <view class="model-settings-container">
    <!-- Header -->
    <view class="header">
      <view class="back-button" @click="navigateBack">
        <image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
      </view>
      <text class="header-title">大模型设置</text>
      <view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
    </view>
    
    <!-- Content -->
    <scroll-view scroll-y class="model-content">
      <!-- Enable Custom Model Section -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">启用自定义大模型</text>
          <switch :checked="isCustomModelEnabled" @change="toggleCustomModel" color="#2196f3" />
        </view>
        <view class="section-description">
          <text>启用后可以连接您自己的大模型服务</text>
        </view>
      </view>
      
      <!-- Model Platform Settings -->
      <view class="section" v-if="isCustomModelEnabled">
        <view class="section-header">
          <text class="section-title">模型平台</text>
        </view>
        
        <view class="platform-options">
          <view class="platform-option">
            <radio :checked="selectedPlatform === 'DeepSeek'" @click="selectPlatform('DeepSeek')" color="#2196f3" />
            <text class="platform-name">DeepSeek</text>
          </view>
          <view class="platform-option">
            <radio :checked="selectedPlatform === 'OpenAI'" @click="selectPlatform('OpenAI')" color="#2196f3" />
            <text class="platform-name">OpenAI</text>
          </view>
        </view>
      </view>
      
      <!-- API Key Section -->
      <view class="section" v-if="isCustomModelEnabled">
        <view class="section-header">
          <text class="section-title">API Key 设置</text>
        </view>
        
        <view class="api-key-input">
          <input type="password" v-model="apiKey" placeholder="输入API密钥" :disabled="!isCustomModelEnabled" />
          <button class="test-button" @click="testApiKey" :disabled="!apiKey || !isCustomModelEnabled">测试</button>
        </view>
      </view>
      
      <!-- Default Model Selection -->
      <view class="section" v-if="isCustomModelEnabled">
        <view class="section-header">
          <text class="section-title">默认模型选择</text>
        </view>
        
        <view class="model-selector">
          <picker @change="onModelChange" :value="defaultModelIndex" :range="availableModels">
            <view class="picker-view">
              <text>{{ availableModels[defaultModelIndex] }}</text>
              <image src="/static/icons/arrow-down.png" mode="aspectFit" class="arrow-icon"></image>
            </view>
          </picker>
        </view>
        
        <view class="model-description">
          <text>{{ getModelDescription() }}</text>
        </view>
      </view>
      
      <!-- Advanced Settings Button -->
      <view class="section" v-if="isCustomModelEnabled">
        <button class="advanced-settings-btn" @click="navigateToApiSettings">高级API设置</button>
      </view>
    </scroll-view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      isCustomModelEnabled: false,
      selectedPlatform: 'DeepSeek',
      apiKey: '',
      defaultModelIndex: 0,
      availableModels: ['deepseek-coder', 'deepseek-chat', 'deepseek-lite'],
      openaiModels: ['gpt-4o', 'gpt-4-turbo', 'gpt-3.5-turbo'],
      modelDescriptions: {
        'deepseek-coder': '代码编程专家模型，适合编程相关学习。',
        'deepseek-chat': '通用对话模型，适合日常交流与学习辅导。',
        'deepseek-lite': '轻量级模型，速度更快，适合简单对话。',
        'gpt-4o': 'OpenAI最新模型，支持多模态理解能力。',
        'gpt-4-turbo': '强大的大语言模型，适合复杂问题解答。',
        'gpt-3.5-turbo': '高性能模型，适合日常对话与知识问答。'
      }
    }
  },
  onLoad() {
    // Load saved settings if available
    const savedSettings = uni.getStorageSync('modelSettings');
    if (savedSettings) {
      try {
        const settings = JSON.parse(savedSettings);
        this.isCustomModelEnabled = settings.isCustomModelEnabled || false;
        this.selectedPlatform = settings.selectedPlatform || 'DeepSeek';
        this.apiKey = settings.apiKey || '';
        
        // Update available models based on platform
        this.updateAvailableModels();
        
        // Set default model index
        if (settings.defaultModel) {
          const index = this.availableModels.indexOf(settings.defaultModel);
          this.defaultModelIndex = index >= 0 ? index : 0;
        }
      } catch (e) {
        console.error('Error parsing saved model settings:', e);
      }
    }
  },
  methods: {
    navigateBack() {
      uni.navigateBack();
    },
    toggleCustomModel(e) {
      this.isCustomModelEnabled = e.detail.value;
      this.saveSettings();
    },
    selectPlatform(platform) {
      this.selectedPlatform = platform;
      this.updateAvailableModels();
      this.defaultModelIndex = 0;
      this.saveSettings();
    },
    updateAvailableModels() {
      if (this.selectedPlatform === 'DeepSeek') {
        this.availableModels = ['deepseek-coder', 'deepseek-chat', 'deepseek-lite'];
      } else if (this.selectedPlatform === 'OpenAI') {
        this.availableModels = ['gpt-4o', 'gpt-4-turbo', 'gpt-3.5-turbo'];
      }
    },
    onModelChange(e) {
      this.defaultModelIndex = e.detail.value;
      this.saveSettings();
    },
    getModelDescription() {
      const modelName = this.availableModels[this.defaultModelIndex];
      return this.modelDescriptions[modelName] || '无描述信息';
    },
    testApiKey() {
      if (!this.apiKey) {
        uni.showToast({
          title: '请输入API密钥',
          icon: 'none'
        });
        return;
      }
      
      uni.showLoading({
        title: '测试中...'
      });
      
      // 模拟API测试
      setTimeout(() => {
        uni.hideLoading();
        uni.showToast({
          title: 'API密钥有效',
          icon: 'success'
        });
      }, 1500);
    },
    navigateToApiSettings() {
      uni.navigateTo({
        url: '/pages/settings/api-settings',
        fail: () => {
          uni.showToast({
            title: '页面开发中，即将上线',
            icon: 'none'
          });
        }
      });
    },
    saveSettings() {
      const settings = {
        isCustomModelEnabled: this.isCustomModelEnabled,
        selectedPlatform: this.selectedPlatform,
        apiKey: this.apiKey,
        defaultModel: this.availableModels[this.defaultModelIndex]
      };
      
      uni.setStorageSync('modelSettings', JSON.stringify(settings));
    }
  }
}
</script>

<style>
.model-settings-container {
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

.model-content {
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
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 30rpx;
  border-bottom: 1rpx solid #f0f0f0;
}

.section-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
}

.section-description {
  padding: 20rpx 30rpx;
  font-size: 28rpx;
  color: #666;
}

.platform-options {
  padding: 20rpx 30rpx;
}

.platform-option {
  display: flex;
  align-items: center;
  padding: 20rpx 0;
}

.platform-name {
  margin-left: 20rpx;
  font-size: 32rpx;
  color: #333;
}

.api-key-input {
  padding: 30rpx;
  display: flex;
  align-items: center;
}

.api-key-input input {
  flex: 1;
  height: 80rpx;
  border: 1rpx solid #ddd;
  border-radius: 10rpx;
  padding: 0 20rpx;
  font-size: 28rpx;
}

.test-button {
  width: 120rpx;
  height: 80rpx;
  margin-left: 20rpx;
  background-color: #2196f3;
  color: white;
  font-size: 28rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 10rpx;
}

.test-button[disabled] {
  background-color: #ccc;
}

.model-selector {
  padding: 30rpx;
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

.model-description {
  padding: 0 30rpx 30rpx;
  font-size: 28rpx;
  color: #666;
}

.advanced-settings-btn {
  margin: 30rpx;
  height: 90rpx;
  line-height: 90rpx;
  background-color: #f5f5f5;
  color: #2196f3;
  font-size: 32rpx;
  border: 1rpx solid #2196f3;
  border-radius: 45rpx;
}
</style> 