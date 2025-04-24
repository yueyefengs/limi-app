<template>
  <view class="parental-controls-container">
    <!-- Header -->
    <view class="header">
      <view class="back-button" @click="navigateBack">
        <image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
      </view>
      <text class="header-title">家长控制</text>
      <view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
    </view>
    
    <!-- Content -->
    <scroll-view scroll-y class="controls-content">
      <!-- PIN Protection Section -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">PIN码保护</text>
          <switch :checked="isPinEnabled" @change="togglePin" color="#2196f3" />
        </view>
        <view class="section-description">
          <text>启用PIN码保护可防止孩子修改家长控制设置</text>
        </view>
        
        <view class="pin-settings" v-if="isPinEnabled">
          <view class="setting-item">
            <text class="item-label">当前PIN码</text>
            <view class="pin-display">
              <text class="pin-dot" v-for="(dot, index) in pinDisplayArray" :key="index">●</text>
            </view>
            <text class="change-button" @click="showPinModal">修改</text>
          </view>
        </view>
      </view>
      
      <!-- Time Controls Section -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">使用时间控制</text>
          <switch :checked="isTimeControlEnabled" @change="toggleTimeControl" color="#2196f3" />
        </view>
        
        <view class="time-controls" v-if="isTimeControlEnabled">
          <!-- Daily Time Limit -->
          <view class="setting-item">
            <text class="item-label">每日使用时长</text>
            <view class="item-value">
              <text>{{ dailyTimeLimit }}分钟</text>
              <image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
            </view>
          </view>
          
          <!-- Bedtime Mode -->
          <view class="setting-item">
            <text class="item-label">休息时间</text>
            <view class="item-value">
              <text>{{ bedtimeStart }} - {{ bedtimeEnd }}</text>
              <image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
            </view>
          </view>
          
          <!-- Break Reminder -->
          <view class="setting-item">
            <text class="item-label">休息提醒</text>
            <view class="item-value">
              <text>每{{ breakInterval }}分钟</text>
              <image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
            </view>
          </view>
          
          <view class="usage-report">
            <view class="report-header">
              <text class="report-title">今日使用情况</text>
              <text class="view-all" @click="viewFullReport">查看完整报告</text>
            </view>
            
            <view class="usage-progress">
              <view class="progress-bar">
                <view class="progress-fill" :style="{width: usagePercentage + '%'}"></view>
              </view>
              <text class="progress-text">{{ usageTime }}/{{ dailyTimeLimit }}分钟</text>
            </view>
            
            <view class="usage-apps">
              <text class="apps-title">使用最多的内容:</text>
              <view class="app-item" v-for="(app, index) in topApps" :key="index">
                <image :src="app.icon" mode="aspectFit" class="app-icon"></image>
                <view class="app-info">
                  <text class="app-name">{{ app.name }}</text>
                  <view class="app-time-bar">
                    <view class="app-time-fill" :style="{width: app.percentage + '%'}"></view>
                  </view>
                </view>
                <text class="app-time">{{ app.time }}分钟</text>
              </view>
            </view>
          </view>
        </view>
      </view>
      
      <!-- Content Controls Section -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">内容过滤</text>
          <switch :checked="isContentFilterEnabled" @change="toggleContentFilter" color="#2196f3" />
        </view>
        
        <view class="content-controls" v-if="isContentFilterEnabled">
          <!-- Age Restriction -->
          <view class="setting-item">
            <text class="item-label">内容年龄限制</text>
            <view class="item-value" @click="showAgeLimitOptions">
              <text>{{ currentAgeLimit }}</text>
              <image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
            </view>
          </view>
          
          <!-- Content Categories -->
          <view class="setting-item">
            <text class="item-label">内容分类限制</text>
            <view class="item-value" @click="navigateTo('/pages/settings/content-categories')">
              <text>已限制{{ blockedCategoriesCount }}类</text>
              <image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
            </view>
          </view>
          
          <!-- Web Filter -->
          <view class="setting-item">
            <text class="item-label">网页浏览限制</text>
            <view class="item-value" @click="navigateTo('/pages/settings/web-filter')">
              <text>{{ webFilterMode }}</text>
              <image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
            </view>
          </view>
          
          <view class="filter-level">
            <text class="level-title">内容过滤级别</text>
            <view class="level-selector">
              <view 
                class="level-option" 
                v-for="(level, index) in filterLevels" 
                :key="index"
                :class="{ active: currentFilterLevel === level.value }"
                @click="setFilterLevel(level.value)"
              >
                <text>{{ level.name }}</text>
              </view>
            </view>
            <text class="level-description">{{ getCurrentFilterDescription() }}</text>
          </view>
        </view>
      </view>
      
      <!-- App Access Section -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">功能访问限制</text>
        </view>
        
        <view class="app-controls">
          <view class="feature-toggle" v-for="(feature, index) in appFeatures" :key="index">
            <view class="feature-info">
              <image :src="feature.icon" mode="aspectFit" class="feature-icon"></image>
              <text class="feature-name">{{ feature.name }}</text>
            </view>
            <switch :checked="feature.enabled" @change="(e) => toggleFeature(index, e)" color="#2196f3" />
          </view>
        </view>
      </view>
    </scroll-view>
    
    <!-- PIN Code Modal -->
    <view class="modal-overlay" v-if="showPinInput">
      <view class="modal-content">
        <view class="modal-header">
          <text class="modal-title">{{ isChangingPin ? '修改PIN码' : '输入PIN码' }}</text>
          <view class="modal-close" @click="cancelPinInput">
            <text>×</text>
          </view>
        </view>
        
        <view class="pin-input-container">
          <text class="pin-instruction">{{ pinInstruction }}</text>
          <view class="pin-dots">
            <view 
              class="pin-input-dot" 
              v-for="i in 4" 
              :key="i" 
              :class="{ filled: currentPin.length >= i }"
            ></view>
          </view>
        </view>
        
        <view class="pin-numpad">
          <view class="numpad-row" v-for="row in numpadRows" :key="row.join('')">
            <view 
              class="numpad-key" 
              v-for="num in row" 
              :key="num" 
              @click="appendToPin(num)"
            >
              <text>{{ num }}</text>
            </view>
          </view>
          <view class="numpad-row">
            <view class="numpad-key numpad-empty"></view>
            <view class="numpad-key" @click="appendToPin('0')">
              <text>0</text>
            </view>
            <view class="numpad-key numpad-delete" @click="deleteFromPin">
              <text>×</text>
            </view>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      // PIN settings
      isPinEnabled: true,
      currentPinCode: '1234', // This would be securely stored in a real app
      pinDisplayArray: [1, 2, 3, 4],
      showPinInput: false,
      isChangingPin: false,
      pinStep: 'current', // 'current', 'new', 'confirm'
      currentPin: '',
      newPin: '',
      pinInstruction: '请输入当前PIN码',
      
      // Time control settings
      isTimeControlEnabled: true,
      dailyTimeLimit: 120,
      bedtimeStart: '20:00',
      bedtimeEnd: '07:00',
      breakInterval: 30,
      usageTime: 45,
      topApps: [
        { name: '数学乐园', icon: '/static/icons/math.png', time: 20, percentage: 80 },
        { name: '英语故事', icon: '/static/icons/english.png', time: 15, percentage: 60 },
        { name: '科学实验', icon: '/static/icons/science.png', time: 10, percentage: 40 }
      ],
      
      // Content filter settings
      isContentFilterEnabled: true,
      currentAgeLimit: '5-8岁',
      blockedCategoriesCount: 3,
      webFilterMode: '仅允许安全网站',
      filterLevels: [
        { name: '低', value: 'low', description: '仅过滤明显不适合儿童的内容' },
        { name: '中', value: 'medium', description: '过滤大部分不适合儿童的内容，适合大多数年龄段的孩子' },
        { name: '高', value: 'high', description: '严格过滤，仅展示专门为儿童设计的内容' }
      ],
      currentFilterLevel: 'medium',
      
      // App features
      appFeatures: [
        { name: '下载内容', icon: '/static/icons/download.png', enabled: true },
        { name: 'AI聊天', icon: '/static/icons/chat.png', enabled: true },
        { name: '分享功能', icon: '/static/icons/share.png', enabled: false },
        { name: '内容评论', icon: '/static/icons/comment.png', enabled: false },
        { name: '应用内购买', icon: '/static/icons/purchase.png', enabled: false }
      ],
      
      // Numpad for PIN entry
      numpadRows: [
        [1, 2, 3],
        [4, 5, 6],
        [7, 8, 9]
      ]
    }
  },
  computed: {
    usagePercentage() {
      return Math.min((this.usageTime / this.dailyTimeLimit) * 100, 100);
    }
  },
  methods: {
    navigateBack() {
      uni.navigateBack();
    },
    navigateTo(url) {
      uni.navigateTo({ url });
    },
    
    // PIN code methods
    togglePin(e) {
      if (e.detail.value) {
        this.showPinModal();
      } else {
        this.verifyPinBeforeDisable();
      }
    },
    showPinModal() {
      this.isChangingPin = true;
      this.pinStep = 'current';
      this.currentPin = '';
      this.pinInstruction = '请输入当前PIN码';
      this.showPinInput = true;
    },
    verifyPinBeforeDisable() {
      this.isChangingPin = false;
      this.pinStep = 'current';
      this.currentPin = '';
      this.pinInstruction = '请输入PIN码以禁用保护';
      this.showPinInput = true;
    },
    cancelPinInput() {
      this.showPinInput = false;
      if (!this.isChangingPin && this.pinStep === 'current') {
        this.isPinEnabled = true; // Revert toggle if canceled
      }
    },
    appendToPin(num) {
      if (this.currentPin.length < 4) {
        this.currentPin += num;
        
        // Check if PIN is complete
        if (this.currentPin.length === 4) {
          setTimeout(() => {
            this.processPinInput();
          }, 300);
        }
      }
    },
    deleteFromPin() {
      if (this.currentPin.length > 0) {
        this.currentPin = this.currentPin.slice(0, -1);
      }
    },
    processPinInput() {
      if (this.pinStep === 'current') {
        // Verify current PIN
        if (this.currentPin === this.currentPinCode) {
          if (this.isChangingPin) {
            // Move to setting new PIN
            this.pinStep = 'new';
            this.newPin = '';
            this.currentPin = '';
            this.pinInstruction = '请输入新的4位PIN码';
          } else {
            // Disable PIN protection
            this.isPinEnabled = false;
            this.showPinInput = false;
          }
        } else {
          // Incorrect PIN
          uni.showToast({
            title: 'PIN码错误',
            icon: 'none'
          });
          this.currentPin = '';
        }
      } else if (this.pinStep === 'new') {
        // Save new PIN and ask to confirm
        this.newPin = this.currentPin;
        this.currentPin = '';
        this.pinStep = 'confirm';
        this.pinInstruction = '请再次输入新PIN码确认';
      } else if (this.pinStep === 'confirm') {
        // Confirm new PIN
        if (this.currentPin === this.newPin) {
          // Update PIN
          this.currentPinCode = this.newPin;
          this.pinDisplayArray = Array.from({length: 4}, (_, i) => i + 1);
          this.showPinInput = false;
          
          uni.showToast({
            title: 'PIN码已更新',
            icon: 'success'
          });
        } else {
          // PINs don't match
          uni.showToast({
            title: 'PIN码不匹配，请重试',
            icon: 'none'
          });
          this.pinStep = 'new';
          this.currentPin = '';
          this.pinInstruction = '请输入新的4位PIN码';
        }
      }
    },
    
    // Time control methods
    toggleTimeControl(e) {
      this.isTimeControlEnabled = e.detail.value;
      if (e.detail.value) {
        uni.showToast({
          title: '已开启使用时间控制',
          icon: 'none'
        });
      } else {
        uni.showToast({
          title: '已关闭使用时间控制',
          icon: 'none'
        });
      }
    },
    viewFullReport() {
      uni.navigateTo({
        url: '/pages/settings/usage-report'
      });
    },
    
    // Content filter methods
    toggleContentFilter(e) {
      this.isContentFilterEnabled = e.detail.value;
    },
    showAgeLimitOptions() {
      uni.showActionSheet({
        itemList: ['0-4岁', '5-8岁', '9-12岁', '13岁以上'],
        success: (res) => {
          const options = ['0-4岁', '5-8岁', '9-12岁', '13岁以上'];
          this.currentAgeLimit = options[res.tapIndex];
        }
      });
    },
    setFilterLevel(level) {
      this.currentFilterLevel = level;
    },
    getCurrentFilterDescription() {
      const level = this.filterLevels.find(l => l.value === this.currentFilterLevel);
      return level ? level.description : '';
    },
    
    // App features methods
    toggleFeature(index, event) {
      this.appFeatures[index].enabled = event.detail.value;
    }
  }
}
</script>

<style>
.parental-controls-container {
  min-height: 100vh;
  background-color: #f5f5f5;
  padding-bottom: env(safe-area-inset-bottom);
}

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 80rpx 40rpx 20rpx;
  background-color: #fff;
}

.back-button {
  padding: 10rpx;
}

.back-icon {
  width: 40rpx;
  height: 40rpx;
}

.header-title {
  font-size: 36rpx;
  font-weight: bold;
  color: #333;
}

.controls-content {
  height: calc(100vh - 150rpx);
}

.section {
  background-color: #fff;
  margin-top: 20rpx;
  padding: 30rpx 40rpx;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.section-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
}

.section-description {
  margin-top: 10rpx;
  font-size: 26rpx;
  color: #999;
}

.setting-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 24rpx 0;
  border-bottom: 1rpx solid #f0f0f0;
}

.setting-item:last-child {
  border-bottom: none;
}

.item-label {
  font-size: 30rpx;
  color: #333;
}

.item-value {
  display: flex;
  align-items: center;
  font-size: 28rpx;
  color: #666;
}

.arrow-icon {
  width: 30rpx;
  height: 30rpx;
  margin-left: 10rpx;
}

.pin-settings {
  margin-top: 20rpx;
}

.pin-display {
  display: flex;
  gap: 10rpx;
}

.pin-dot {
  font-size: 30rpx;
  color: #333;
}

.change-button {
  margin-left: 20rpx;
  color: #2196f3;
  font-size: 28rpx;
}

/* Time Controls */
.time-controls {
  margin-top: 20rpx;
}

.usage-report {
  margin-top: 30rpx;
  background-color: #f9f9f9;
  border-radius: 16rpx;
  padding: 20rpx;
}

.report-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20rpx;
}

.report-title {
  font-size: 28rpx;
  font-weight: 500;
  color: #333;
}

.view-all {
  font-size: 26rpx;
  color: #2196f3;
}

.usage-progress {
  margin-bottom: 30rpx;
}

.progress-bar {
  height: 16rpx;
  background-color: #e0e0e0;
  border-radius: 8rpx;
  margin-bottom: 10rpx;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background-color: #2196f3;
  border-radius: 8rpx;
}

.progress-text {
  font-size: 26rpx;
  color: #666;
  text-align: right;
}

.usage-apps {
  margin-top: 20rpx;
}

.apps-title {
  font-size: 26rpx;
  color: #666;
  margin-bottom: 15rpx;
  display: block;
}

.app-item {
  display: flex;
  align-items: center;
  margin-bottom: 15rpx;
}

.app-icon {
  width: 40rpx;
  height: 40rpx;
  margin-right: 15rpx;
}

.app-info {
  flex: 1;
}

.app-name {
  font-size: 26rpx;
  color: #333;
  margin-bottom: 6rpx;
  display: block;
}

.app-time-bar {
  height: 8rpx;
  background-color: #e0e0e0;
  border-radius: 4rpx;
  overflow: hidden;
  width: 100%;
}

.app-time-fill {
  height: 100%;
  background-color: #2196f3;
  border-radius: 4rpx;
}

.app-time {
  font-size: 24rpx;
  color: #666;
  margin-left: 15rpx;
  min-width: 60rpx;
  text-align: right;
}

/* Content Controls */
.content-controls {
  margin-top: 20rpx;
}

.filter-level {
  margin-top: 30rpx;
}

.level-title {
  font-size: 28rpx;
  color: #333;
  margin-bottom: 15rpx;
  display: block;
}

.level-selector {
  display: flex;
  margin-bottom: 15rpx;
}

.level-option {
  flex: 1;
  text-align: center;
  padding: 16rpx 0;
  background-color: #f0f0f0;
  font-size: 28rpx;
  color: #666;
}

.level-option:first-child {
  border-top-left-radius: 8rpx;
  border-bottom-left-radius: 8rpx;
}

.level-option:last-child {
  border-top-right-radius: 8rpx;
  border-bottom-right-radius: 8rpx;
}

.level-option.active {
  background-color: #2196f3;
  color: white;
}

.level-description {
  font-size: 26rpx;
  color: #666;
  display: block;
}

/* App Controls */
.app-controls {
  margin-top: 20rpx;
}

.feature-toggle {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20rpx 0;
  border-bottom: 1rpx solid #f0f0f0;
}

.feature-toggle:last-child {
  border-bottom: none;
}

.feature-info {
  display: flex;
  align-items: center;
}

.feature-icon {
  width: 40rpx;
  height: 40rpx;
  margin-right: 20rpx;
}

.feature-name {
  font-size: 30rpx;
  color: #333;
}

/* PIN Code Modal */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 999;
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  width: 600rpx;
  background-color: white;
  border-radius: 20rpx;
  padding: 40rpx;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 40rpx;
}

.modal-title {
  font-size: 36rpx;
  font-weight: bold;
  color: #333;
}

.modal-close {
  font-size: 40rpx;
  color: #999;
  padding: 10rpx;
}

.pin-input-container {
  margin-bottom: 40rpx;
}

.pin-instruction {
  font-size: 30rpx;
  color: #666;
  text-align: center;
  margin-bottom: 30rpx;
}

.pin-dots {
  display: flex;
  justify-content: center;
  gap: 30rpx;
  margin-bottom: 40rpx;
}

.pin-input-dot {
  width: 30rpx;
  height: 30rpx;
  border-radius: 15rpx;
  border: 2rpx solid #ccc;
}

.pin-input-dot.filled {
  background-color: #2196f3;
  border-color: #2196f3;
}

.pin-numpad {
  display: flex;
  flex-direction: column;
  gap: 20rpx;
}

.numpad-row {
  display: flex;
  justify-content: space-between;
}

.numpad-key {
  width: 120rpx;
  height: 120rpx;
  border-radius: 60rpx;
  background-color: #f5f5f5;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 40rpx;
  color: #333;
}

.numpad-empty {
  background-color: transparent;
}

.numpad-delete {
  font-size: 36rpx;
  color: #ff3b30;
}
</style> 