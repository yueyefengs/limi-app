<template>
  <view class="api-settings-container">
    <!-- Header -->
    <view class="header">
      <view class="back-button" @click="navigateBack">
        <image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
      </view>
      <text class="header-title">大模型API设置</text>
      <view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
    </view>
    
    <!-- Content -->
    <scroll-view scroll-y class="api-content">
      <!-- Model Type Selection -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">大模型类型</text>
        </view>
        
        <view class="model-type-selector">
          <picker @change="onModelTypeChange" :value="modelTypeIndex" :range="modelTypes">
            <view class="picker-view">
              <text>{{ modelTypes[modelTypeIndex] }}</text>
              <image src="/static/icons/arrow-down.png" mode="aspectFit" class="arrow-icon"></image>
            </view>
          </picker>
        </view>
      </view>
      
      <!-- API Key Section -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">API密钥</text>
        </view>
        
        <view class="api-key-input">
          <input type="password" v-model="apiKey" placeholder="输入API密钥" />
          <button class="test-button" @click="testApiKey" :disabled="!apiKey">测试</button>
        </view>
        
        <view class="api-key-note">
          <text>API密钥用于访问大模型服务，请保管好您的密钥信息</text>
        </view>
      </view>
      
      <!-- API Endpoint Section -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">API端点 (高级设置)</text>
        </view>
        
        <view class="api-endpoint-input">
          <input type="text" v-model="apiEndpoint" placeholder="https://api.openai.com/v1" />
        </view>
        
        <view class="endpoint-note">
          <text>仅在使用自托管服务或自定义服务器时需要修改</text>
        </view>
      </view>
      
      <!-- Default Model Selection -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">默认模型</text>
        </view>
        
        <view class="model-selector">
          <picker @change="onDefaultModelChange" :value="defaultModelIndex" :range="availableModels">
            <view class="picker-view">
              <text>{{ availableModels[defaultModelIndex] }}</text>
              <image src="/static/icons/arrow-down.png" mode="aspectFit" class="arrow-icon"></image>
            </view>
          </picker>
        </view>
        
        <view class="model-note">
          <text>选择默认使用的模型，不同模型有不同的能力和资源标准</text>
        </view>
      </view>
      
      <!-- Save Button -->
      <button class="save-button" @click="saveSettings">保存设置</button>
    </scroll-view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      modelTypeIndex: 0,
      modelTypes: ['OpenAI (GPT系列)', 'DeepSeek (深度求索)', 'Anthropic (Claude系列)', '其他'],
      apiKey: '',
      apiEndpoint: 'https://api.openai.com/v1',
      defaultModelIndex: 0,
      availableModels: ['GPT-4o', 'GPT-4-turbo', 'GPT-3.5-turbo'],
      modelsByType: {
        'OpenAI (GPT系列)': ['GPT-4o', 'GPT-4-turbo', 'GPT-3.5-turbo'],
        'DeepSeek (深度求索)': ['deepseek-coder', 'deepseek-chat', 'deepseek-lite'],
        'Anthropic (Claude系列)': ['claude-3-opus', 'claude-3-sonnet', 'claude-2'],
        '其他': ['自定义模型']
      },
      endpointsByType: {
        'OpenAI (GPT系列)': 'https://api.openai.com/v1',
        'DeepSeek (深度求索)': 'https://api.deepseek.com/v1',
        'Anthropic (Claude系列)': 'https://api.anthropic.com/v1',
        '其他': ''
      }
    }
  },
  onLoad() {
    // Load saved settings if available
    const savedSettings = uni.getStorageSync('apiSettings');
    if (savedSettings) {
      try {
        const settings = JSON.parse(savedSettings);
        
        // Set model type
        if (settings.modelType) {
          const index = this.modelTypes.indexOf(settings.modelType);
          this.modelTypeIndex = index >= 0 ? index : 0;
        }
        
        this.apiKey = settings.apiKey || '';
        this.apiEndpoint = settings.apiEndpoint || this.endpointsByType[this.modelTypes[this.modelTypeIndex]];
        
        // Update available models based on model type
        this.updateAvailableModels();
        
        // Set default model
        if (settings.defaultModel) {
          const index = this.availableModels.indexOf(settings.defaultModel);
          this.defaultModelIndex = index >= 0 ? index : 0;
        }
      } catch (e) {
        console.error('Error parsing saved API settings:', e);
      }
    } else {
      // Initialize with default endpoint for selected model type
      this.apiEndpoint = this.endpointsByType[this.modelTypes[this.modelTypeIndex]];
    }
  },
  methods: {
    navigateBack() {
      uni.navigateBack();
    },
    onModelTypeChange(e) {
      this.modelTypeIndex = e.detail.value;
      this.updateAvailableModels();
      this.defaultModelIndex = 0;
      
      // Update API endpoint based on model type if not customized
      const currentModelType = this.modelTypes[this.modelTypeIndex];
      this.apiEndpoint = this.endpointsByType[currentModelType];
    },
    updateAvailableModels() {
      const modelType = this.modelTypes[this.modelTypeIndex];
      this.availableModels = this.modelsByType[modelType] || [];
    },
    onDefaultModelChange(e) {
      this.defaultModelIndex = e.detail.value;
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
    saveSettings() {
      const settings = {
        modelType: this.modelTypes[this.modelTypeIndex],
        apiKey: this.apiKey,
        apiEndpoint: this.apiEndpoint,
        defaultModel: this.availableModels[this.defaultModelIndex]
      };
      
      uni.setStorageSync('apiSettings', JSON.stringify(settings));
      
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
.api-settings-container {
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

.api-content {
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

.model-type-selector,
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

.api-key-input,
.api-endpoint-input {
  padding: 30rpx;
  display: flex;
  align-items: center;
}

.api-key-input input,
.api-endpoint-input input {
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

.api-key-note,
.endpoint-note,
.model-note {
  padding: 0 30rpx 30rpx;
  font-size: 28rpx;
  color: #666;
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