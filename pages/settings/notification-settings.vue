<template>
  <view class="container">
    <!-- Header -->
    <view class="header">
      <view class="back-button" @click="navigateBack">
        <text class="back-icon">←</text>
      </view>
      <text class="header-title">通知设置</text>
    </view>

    <!-- Main Content -->
    <scroll-view scroll-y class="content">
      <!-- Information Banner -->
      <view class="info-banner">
        <text class="info-text">在这里管理应用通知，确保重要信息不会被错过，同时避免过多干扰</text>
      </view>

      <!-- General Notifications -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">通知开关</text>
        </view>
        <view class="setting-card">
          <view class="setting-item">
            <view class="setting-info">
              <text class="setting-title">推送通知</text>
              <text class="setting-desc">开启或关闭所有应用通知</text>
            </view>
            <switch :checked="notificationsEnabled" @change="toggleNotifications" color="#4F6BED" />
          </view>
        </view>
      </view>

      <!-- Notification Types (Only shown if notifications are enabled) -->
      <view v-if="notificationsEnabled" class="section">
        <view class="section-header">
          <text class="section-title">通知类型</text>
        </view>
        <view class="setting-card">
          <view class="setting-item" v-for="(item, index) in notificationTypes" :key="index">
            <view class="setting-info">
              <text class="setting-title">{{item.title}}</text>
              <text class="setting-desc">{{item.description}}</text>
            </view>
            <switch :checked="item.enabled" @change="(e) => toggleNotificationType(index, e)" color="#4F6BED" />
          </view>
        </view>
      </view>

      <!-- Quiet Hours (Only shown if notifications are enabled) -->
      <view v-if="notificationsEnabled" class="section">
        <view class="section-header">
          <text class="section-title">勿扰时段</text>
        </view>
        <view class="setting-card">
          <view class="setting-item">
            <view class="setting-info">
              <text class="setting-title">开启勿扰时段</text>
              <text class="setting-desc">在指定时间段内禁用通知提醒</text>
            </view>
            <switch :checked="quietHoursEnabled" @change="toggleQuietHours" color="#4F6BED" />
          </view>
          
          <view v-if="quietHoursEnabled" class="time-settings">
            <view class="time-setting">
              <text class="time-label">开始时间</text>
              <picker mode="time" :value="quietHoursStart" @change="onStartTimeChange" class="time-picker">
                <view class="picker-text">
                  <text>{{quietHoursStart}}</text>
                </view>
              </picker>
            </view>
            <view class="time-setting">
              <text class="time-label">结束时间</text>
              <picker mode="time" :value="quietHoursEnd" @change="onEndTimeChange" class="time-picker">
                <view class="picker-text">
                  <text>{{quietHoursEnd}}</text>
                </view>
              </picker>
            </view>
          </view>
        </view>
      </view>

      <!-- Sound and Vibration Settings -->
      <view v-if="notificationsEnabled" class="section">
        <view class="section-header">
          <text class="section-title">提示方式</text>
        </view>
        <view class="setting-card">
          <view class="setting-item">
            <view class="setting-info">
              <text class="setting-title">声音</text>
              <text class="setting-desc">通知接收时播放提示音</text>
            </view>
            <switch :checked="soundEnabled" @change="toggleSound" color="#4F6BED" />
          </view>
          <view class="setting-item">
            <view class="setting-info">
              <text class="setting-title">振动</text>
              <text class="setting-desc">通知接收时振动提醒</text>
            </view>
            <switch :checked="vibrationEnabled" @change="toggleVibration" color="#4F6BED" />
          </view>
        </view>
      </view>

      <!-- App Update Notifications -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">应用更新</text>
        </view>
        <view class="setting-card">
          <view class="setting-item">
            <view class="setting-info">
              <text class="setting-title">自动更新通知</text>
              <text class="setting-desc">有新版本时通知提醒</text>
            </view>
            <switch :checked="updateNotificationsEnabled" @change="toggleUpdateNotifications" color="#4F6BED" />
          </view>
        </view>
      </view>
    </scroll-view>

    <!-- Save Button -->
    <view class="bottom-button">
      <button class="save-button" @click="saveSettings">保存设置</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      notificationsEnabled: true,
      notificationTypes: [
        {
          title: '学习提醒',
          description: '课程和学习活动的提醒',
          enabled: true
        },
        {
          title: '新内容推送',
          description: '有新内容上线时通知',
          enabled: true
        },
        {
          title: '活动通知',
          description: '特别活动和挑战的通知',
          enabled: true
        },
        {
          title: '成就通知',
          description: '解锁成就和获得奖励时提醒',
          enabled: true
        },
        {
          title: '聊天通知',
          description: 'AI助手有新消息时提醒',
          enabled: true
        }
      ],
      quietHoursEnabled: false,
      quietHoursStart: '21:00',
      quietHoursEnd: '07:00',
      soundEnabled: true,
      vibrationEnabled: true,
      updateNotificationsEnabled: true
    }
  },
  onLoad() {
    // Load saved notification settings
    this.loadSettings()
  },
  methods: {
    navigateBack() {
      uni.navigateBack()
    },
    loadSettings() {
      // Mock function to load saved notification settings
      // In a real app, this would likely fetch from storage or API
      const settings = uni.getStorageSync('notificationSettings')
      if (settings) {
        try {
          const parsedSettings = JSON.parse(settings)
          this.notificationsEnabled = parsedSettings.notificationsEnabled
          this.notificationTypes = parsedSettings.notificationTypes
          this.quietHoursEnabled = parsedSettings.quietHoursEnabled
          this.quietHoursStart = parsedSettings.quietHoursStart
          this.quietHoursEnd = parsedSettings.quietHoursEnd
          this.soundEnabled = parsedSettings.soundEnabled
          this.vibrationEnabled = parsedSettings.vibrationEnabled
          this.updateNotificationsEnabled = parsedSettings.updateNotificationsEnabled
        } catch (e) {
          console.error('Failed to parse notification settings', e)
        }
      }
    },
    toggleNotifications(e) {
      this.notificationsEnabled = e.detail.value
    },
    toggleNotificationType(index, e) {
      this.notificationTypes[index].enabled = e.detail.value
    },
    toggleQuietHours(e) {
      this.quietHoursEnabled = e.detail.value
    },
    onStartTimeChange(e) {
      this.quietHoursStart = e.detail.value
    },
    onEndTimeChange(e) {
      this.quietHoursEnd = e.detail.value
    },
    toggleSound(e) {
      this.soundEnabled = e.detail.value
    },
    toggleVibration(e) {
      this.vibrationEnabled = e.detail.value
    },
    toggleUpdateNotifications(e) {
      this.updateNotificationsEnabled = e.detail.value
    },
    saveSettings() {
      // Save notification settings
      const settings = {
        notificationsEnabled: this.notificationsEnabled,
        notificationTypes: this.notificationTypes,
        quietHoursEnabled: this.quietHoursEnabled,
        quietHoursStart: this.quietHoursStart,
        quietHoursEnd: this.quietHoursEnd,
        soundEnabled: this.soundEnabled,
        vibrationEnabled: this.vibrationEnabled,
        updateNotificationsEnabled: this.updateNotificationsEnabled
      }
      
      uni.setStorageSync('notificationSettings', JSON.stringify(settings))
      
      uni.showToast({
        title: '设置已保存',
        icon: 'success'
      })
    }
  }
}
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  background-color: #f5f6fa;
}

.header {
  display: flex;
  align-items: center;
  padding: 15px;
  background-color: #ffffff;
  position: relative;
  z-index: 10;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
}

.back-button {
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.back-icon {
  font-size: 24px;
  font-weight: bold;
  color: #333;
}

.header-title {
  flex: 1;
  text-align: center;
  font-size: 18px;
  font-weight: bold;
  color: #333;
  margin-right: 40px;
}

.content {
  flex: 1;
  padding: 15px;
}

.info-banner {
  background-color: #E9EFFF;
  border-radius: 12px;
  padding: 15px;
  margin-bottom: 20px;
}

.info-text {
  color: #4F6BED;
  font-size: 14px;
  line-height: 1.4;
}

.section {
  margin-bottom: 20px;
}

.section-header {
  margin-bottom: 10px;
}

.section-title {
  font-size: 16px;
  font-weight: bold;
  color: #333;
}

.setting-card {
  background-color: #ffffff;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.setting-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  border-bottom: 1px solid #f0f0f0;
}

.setting-item:last-child {
  border-bottom: none;
}

.setting-info {
  flex: 1;
}

.setting-title {
  font-size: 16px;
  color: #333;
  margin-bottom: 4px;
}

.setting-desc {
  font-size: 13px;
  color: #888;
}

.time-settings {
  padding: 0 15px 15px;
  display: flex;
  flex-direction: column;
  border-top: 1px dashed #f0f0f0;
  margin-top: 5px;
}

.time-setting {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 15px;
}

.time-label {
  font-size: 14px;
  color: #555;
}

.time-picker {
  width: 120px;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  padding: 8px 12px;
  text-align: center;
}

.picker-text {
  color: #333;
  font-size: 14px;
}

.bottom-button {
  padding: 15px;
  background-color: #ffffff;
  box-shadow: 0 -2px 4px rgba(0, 0, 0, 0.05);
}

.save-button {
  background-color: #4F6BED;
  color: white;
  border-radius: 25px;
  font-size: 16px;
  font-weight: bold;
  padding: 12px;
  border: none;
}
</style> 