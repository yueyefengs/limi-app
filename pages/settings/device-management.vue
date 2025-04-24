<template>
  <view class="device-management-container">
    <!-- Header -->
    <view class="header">
      <view class="back-button" @click="navigateBack">
        <image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
      </view>
      <text class="header-title">设备管理</text>
      <view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
    </view>
    
    <!-- Content -->
    <scroll-view scroll-y class="management-content">
      <!-- Current Device Info -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">当前设备</text>
        </view>
        
        <view class="current-device">
          <view class="device-icon-wrapper">
            <image :src="currentDevice.icon" mode="aspectFit" class="device-icon"></image>
          </view>
          <view class="device-info">
            <text class="device-name">{{ currentDevice.name }}</text>
            <text class="device-model">{{ currentDevice.model }}</text>
            <view class="device-status">
              <view class="status-dot"></view>
              <text class="status-text">当前使用中</text>
            </view>
          </view>
        </view>
        
        <view class="device-details">
          <view class="detail-row">
            <text class="detail-label">设备ID</text>
            <text class="detail-value">{{ currentDevice.id }}</text>
          </view>
          <view class="detail-row">
            <text class="detail-label">操作系统</text>
            <text class="detail-value">{{ currentDevice.os }}</text>
          </view>
          <view class="detail-row">
            <text class="detail-label">应用版本</text>
            <text class="detail-value">{{ currentDevice.appVersion }}</text>
          </view>
          <view class="detail-row">
            <text class="detail-label">最后登录</text>
            <text class="detail-value">{{ currentDevice.lastLogin }}</text>
          </view>
        </view>
      </view>
      
      <!-- Connected Devices -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">已连接设备</text>
          <text class="add-device" @click="showAddDeviceModal">添加设备</text>
        </view>
        
        <view class="device-list">
          <view 
            class="device-item" 
            v-for="(device, index) in connectedDevices" 
            :key="index"
          >
            <view class="device-item-left">
              <image :src="device.icon" mode="aspectFit" class="device-item-icon"></image>
              <view class="device-item-info">
                <text class="device-item-name">{{ device.name }}</text>
                <text class="device-item-model">{{ device.model }}</text>
                <text class="device-item-time">上次活跃: {{ device.lastActive }}</text>
              </view>
            </view>
            <view class="device-actions">
              <view class="device-action" @click="showDeviceOptions(index)">
                <image src="/static/icons/more.png" mode="aspectFit" class="action-icon"></image>
              </view>
            </view>
          </view>
        </view>
        
        <view v-if="connectedDevices.length === 0" class="empty-devices">
          <image src="/static/images/empty-devices.png" mode="aspectFit" class="empty-image"></image>
          <text class="empty-text">暂无其他已连接设备</text>
        </view>
      </view>
      
      <!-- App Version Update -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">应用版本</text>
        </view>
        
        <view class="version-info">
          <view class="version-current">
            <text class="version-label">当前版本</text>
            <text class="version-number">{{ appVersion }}</text>
          </view>
          
          <view class="version-status" v-if="hasUpdate">
            <view class="update-message">
              <text class="update-text">发现新版本 {{ latestVersion }}</text>
              <text class="update-features">{{ updateFeatures }}</text>
            </view>
            <button class="update-button" @click="updateApp">立即更新</button>
          </view>
          <view class="version-status" v-else>
            <view class="update-message">
              <text class="update-text">当前已是最新版本</text>
            </view>
            <button class="check-button" @click="checkForUpdates">检查更新</button>
          </view>
        </view>
      </view>
      
      <!-- Data Backup -->
      <view class="section">
        <view class="section-header">
          <text class="section-title">数据备份与同步</text>
        </view>
        
        <view class="backup-info">
          <view class="backup-status">
            <text class="backup-label">上次备份</text>
            <text class="backup-time">{{ lastBackupTime || '从未备份' }}</text>
          </view>
          
          <view class="backup-buttons">
            <button class="backup-button" @click="backupData">
              <image src="/static/icons/backup.png" mode="aspectFit" class="backup-icon"></image>
              <text>立即备份</text>
            </button>
            <button class="restore-button" @click="restoreData">
              <image src="/static/icons/restore.png" mode="aspectFit" class="restore-icon"></image>
              <text>恢复数据</text>
            </button>
          </view>
          
          <view class="auto-backup">
            <text class="auto-backup-label">自动备份</text>
            <switch :checked="isAutoBackupEnabled" @change="toggleAutoBackup" color="#2196f3" />
          </view>
        </view>
      </view>
    </scroll-view>
    
    <!-- Add Device Modal -->
    <view class="modal-overlay" v-if="showAddModal">
      <view class="modal-content">
        <view class="modal-header">
          <text class="modal-title">添加新设备</text>
          <view class="modal-close" @click="closeAddModal">
            <text>×</text>
          </view>
        </view>
        
        <view class="add-device-content">
          <text class="instruction-text">在新设备上打开小智App，进入登录界面，使用扫码登录功能扫描下方二维码：</text>
          
          <view class="qrcode-container">
            <image src="/static/images/qrcode.png" mode="aspectFit" class="qrcode-image"></image>
          </view>
          
          <text class="qrcode-hint">二维码有效期30分钟</text>
          
          <view class="device-limits">
            <text class="limits-title">设备限制</text>
            <text class="limits-description">您的账户最多可以关联5台设备，当前已关联{{ connectedDevices.length + 1 }}台</text>
          </view>
        </view>
      </view>
    </view>
    
    <!-- Device Options Modal -->
    <view class="action-sheet" v-if="showDeviceActionSheet">
      <view class="action-sheet-content">
        <view class="action-sheet-title">
          <text>{{ selectedDevice ? selectedDevice.name : '' }}</text>
        </view>
        
        <view class="action-item" @click="viewDeviceInfo">
          <text>查看详情</text>
        </view>
        
        <view class="action-item" @click="renameDevice">
          <text>重命名设备</text>
        </view>
        
        <view class="action-item warning" @click="logoutDevice">
          <text>退出登录</text>
        </view>
        
        <view class="action-item danger" @click="removeDevice">
          <text>移除设备</text>
        </view>
      </view>
      
      <view class="action-sheet-cancel" @click="closeActionSheet">
        <text>取消</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      // Current device
      currentDevice: {
        id: 'DEV123456789',
        name: '我的iPad',
        model: 'iPad Pro 11英寸',
        os: 'iOS 16.5',
        icon: '/static/icons/tablet.png',
        appVersion: 'v1.0.0',
        lastLogin: '当前在线'
      },
      
      // Connected devices
      connectedDevices: [
        {
          id: 'DEV987654321',
          name: '家庭电脑',
          model: 'Windows 11',
          icon: '/static/icons/laptop.png',
          lastActive: '2023-06-15 20:30'
        },
        {
          id: 'DEV456789123',
          name: '妈妈的手机',
          model: 'iPhone 14',
          icon: '/static/icons/smartphone.png',
          lastActive: '2023-06-16 08:15'
        }
      ],
      
      // App version
      appVersion: 'v1.0.0',
      hasUpdate: true,
      latestVersion: 'v1.1.0',
      updateFeatures: '新增课程内容、优化用户界面、修复已知问题',
      
      // Backup
      lastBackupTime: '2023-06-15 18:30',
      isAutoBackupEnabled: true,
      
      // Modals
      showAddModal: false,
      showDeviceActionSheet: false,
      selectedDeviceIndex: -1
    }
  },
  computed: {
    selectedDevice() {
      if (this.selectedDeviceIndex >= 0 && this.selectedDeviceIndex < this.connectedDevices.length) {
        return this.connectedDevices[this.selectedDeviceIndex];
      }
      return null;
    }
  },
  methods: {
    navigateBack() {
      uni.navigateBack();
    },
    
    // Device management
    showAddDeviceModal() {
      this.showAddModal = true;
    },
    
    closeAddModal() {
      this.showAddModal = false;
    },
    
    showDeviceOptions(index) {
      this.selectedDeviceIndex = index;
      this.showDeviceActionSheet = true;
    },
    
    closeActionSheet() {
      this.showDeviceActionSheet = false;
    },
    
    viewDeviceInfo() {
      uni.showToast({
        title: '查看设备详情',
        icon: 'none'
      });
      this.closeActionSheet();
    },
    
    renameDevice() {
      uni.showModal({
        title: '重命名设备',
        editable: true,
        placeholderText: this.selectedDevice.name,
        success: (res) => {
          if (res.confirm && res.content) {
            this.connectedDevices[this.selectedDeviceIndex].name = res.content;
            uni.showToast({
              title: '重命名成功',
              icon: 'success'
            });
          }
        }
      });
      this.closeActionSheet();
    },
    
    logoutDevice() {
      uni.showModal({
        title: '退出登录',
        content: `确定要退出 ${this.selectedDevice.name} 的登录吗？`,
        success: (res) => {
          if (res.confirm) {
            uni.showToast({
              title: '已退出登录',
              icon: 'success'
            });
          }
        }
      });
      this.closeActionSheet();
    },
    
    removeDevice() {
      uni.showModal({
        title: '移除设备',
        content: `确定要移除 ${this.selectedDevice.name} 吗？移除后将无法访问此设备上的本地数据。`,
        confirmColor: '#ff3b30',
        success: (res) => {
          if (res.confirm) {
            this.connectedDevices.splice(this.selectedDeviceIndex, 1);
            uni.showToast({
              title: '设备已移除',
              icon: 'success'
            });
          }
        }
      });
      this.closeActionSheet();
    },
    
    // App updates
    checkForUpdates() {
      uni.showLoading({
        title: '检查更新中...'
      });
      
      // Simulate checking for updates
      setTimeout(() => {
        uni.hideLoading();
        if (!this.hasUpdate) {
          // Randomly decide if there's an update available
          const hasNewUpdate = Math.random() > 0.5;
          if (hasNewUpdate) {
            this.hasUpdate = true;
            uni.showToast({
              title: '发现新版本',
              icon: 'success'
            });
          } else {
            uni.showToast({
              title: '当前已是最新版本',
              icon: 'none'
            });
          }
        } else {
          uni.showToast({
            title: '发现新版本',
            icon: 'success'
          });
        }
      }, 1500);
    },
    
    updateApp() {
      uni.showLoading({
        title: '准备更新...'
      });
      
      // Simulate update download
      setTimeout(() => {
        uni.hideLoading();
        uni.showModal({
          title: '应用更新',
          content: '新版本已下载完成，是否立即安装？',
          success: (res) => {
            if (res.confirm) {
              uni.showToast({
                title: '正在安装...',
                icon: 'loading',
                duration: 2000
              });
            }
          }
        });
      }, 2000);
    },
    
    // Backup and restore
    toggleAutoBackup(e) {
      this.isAutoBackupEnabled = e.detail.value;
    },
    
    backupData() {
      uni.showLoading({
        title: '备份中...'
      });
      
      // Simulate backup process
      setTimeout(() => {
        uni.hideLoading();
        const now = new Date();
        this.lastBackupTime = `${now.getFullYear()}-${String(now.getMonth() + 1).padStart(2, '0')}-${String(now.getDate()).padStart(2, '0')} ${String(now.getHours()).padStart(2, '0')}:${String(now.getMinutes()).padStart(2, '0')}`;
        uni.showToast({
          title: '备份成功',
          icon: 'success'
        });
      }, 2000);
    },
    
    restoreData() {
      uni.showModal({
        title: '恢复数据',
        content: '确定要从云端恢复数据吗？这将覆盖当前设备上的数据。',
        success: (res) => {
          if (res.confirm) {
            uni.showLoading({
              title: '恢复中...'
            });
            
            // Simulate restore process
            setTimeout(() => {
              uni.hideLoading();
              uni.showToast({
                title: '恢复成功',
                icon: 'success'
              });
            }, 2000);
          }
        }
      });
    }
  }
}
</script>

<style>
.device-management-container {
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

.management-content {
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
  margin-bottom: 30rpx;
}

.section-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
}

.add-device {
  font-size: 28rpx;
  color: #2196f3;
}

/* Current Device */
.current-device {
  display: flex;
  align-items: center;
  margin-bottom: 30rpx;
}

.device-icon-wrapper {
  width: 100rpx;
  height: 100rpx;
  background-color: #f0f7ff;
  border-radius: 20rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 30rpx;
}

.device-icon {
  width: 60rpx;
  height: 60rpx;
}

.device-info {
  flex: 1;
}

.device-name {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
  margin-bottom: 8rpx;
  display: block;
}

.device-model {
  font-size: 26rpx;
  color: #666;
  margin-bottom: 10rpx;
  display: block;
}

.device-status {
  display: flex;
  align-items: center;
}

.status-dot {
  width: 12rpx;
  height: 12rpx;
  border-radius: 6rpx;
  background-color: #4caf50;
  margin-right: 8rpx;
}

.status-text {
  font-size: 24rpx;
  color: #4caf50;
}

.device-details {
  background-color: #f8f8f8;
  border-radius: 16rpx;
  padding: 20rpx;
}

.detail-row {
  display: flex;
  justify-content: space-between;
  padding: 15rpx 0;
  border-bottom: 1rpx solid #f0f0f0;
}

.detail-row:last-child {
  border-bottom: none;
}

.detail-label {
  font-size: 26rpx;
  color: #666;
}

.detail-value {
  font-size: 26rpx;
  color: #333;
}

/* Connected Devices */
.device-list {
  margin-top: 20rpx;
}

.device-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20rpx 0;
  border-bottom: 1rpx solid #f0f0f0;
}

.device-item:last-child {
  border-bottom: none;
}

.device-item-left {
  display: flex;
  align-items: center;
}

.device-item-icon {
  width: 70rpx;
  height: 70rpx;
  margin-right: 20rpx;
}

.device-item-info {
  display: flex;
  flex-direction: column;
}

.device-item-name {
  font-size: 28rpx;
  font-weight: 500;
  color: #333;
  margin-bottom: 6rpx;
}

.device-item-model {
  font-size: 24rpx;
  color: #666;
  margin-bottom: 6rpx;
}

.device-item-time {
  font-size: 22rpx;
  color: #999;
}

.device-actions {
  display: flex;
}

.device-action {
  width: 60rpx;
  height: 60rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.action-icon {
  width: 40rpx;
  height: 40rpx;
}

.empty-devices {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 60rpx 0;
}

.empty-image {
  width: 200rpx;
  height: 200rpx;
  margin-bottom: 30rpx;
}

.empty-text {
  font-size: 28rpx;
  color: #999;
}

/* Version Info */
.version-info {
  background-color: #f8f8f8;
  border-radius: 16rpx;
  padding: 30rpx;
}

.version-current {
  display: flex;
  justify-content: space-between;
  margin-bottom: 30rpx;
}

.version-label {
  font-size: 28rpx;
  color: #666;
}

.version-number {
  font-size: 28rpx;
  color: #333;
  font-weight: 500;
}

.version-status {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.update-message {
  flex: 1;
}

.update-text {
  font-size: 28rpx;
  color: #333;
  display: block;
  margin-bottom: 8rpx;
}

.update-features {
  font-size: 24rpx;
  color: #666;
  display: block;
}

.update-button, .check-button {
  padding: 0 30rpx;
  height: 70rpx;
  line-height: 70rpx;
  border-radius: 35rpx;
  font-size: 28rpx;
}

.update-button {
  background-color: #2196f3;
  color: white;
}

.check-button {
  background-color: #f5f5f5;
  color: #333;
}

/* Backup and Restore */
.backup-info {
  background-color: #f8f8f8;
  border-radius: 16rpx;
  padding: 30rpx;
}

.backup-status {
  display: flex;
  justify-content: space-between;
  margin-bottom: 30rpx;
}

.backup-label {
  font-size: 28rpx;
  color: #666;
}

.backup-time {
  font-size: 28rpx;
  color: #333;
}

.backup-buttons {
  display: flex;
  justify-content: space-between;
  margin-bottom: 30rpx;
}

.backup-button, .restore-button {
  width: 48%;
  height: 90rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 16rpx;
  font-size: 28rpx;
}

.backup-button {
  background-color: #2196f3;
  color: white;
}

.restore-button {
  background-color: #f5f5f5;
  color: #333;
}

.backup-icon, .restore-icon {
  width: 36rpx;
  height: 36rpx;
  margin-right: 10rpx;
}

.auto-backup {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-top: 20rpx;
  border-top: 1rpx solid #eee;
}

.auto-backup-label {
  font-size: 28rpx;
  color: #333;
}

/* Add Device Modal */
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
  width: 80%;
  background-color: white;
  border-radius: 16rpx;
  padding: 30rpx;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30rpx;
}

.modal-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
}

.modal-close {
  font-size: 40rpx;
  color: #999;
  padding: 10rpx;
}

.add-device-content {
  padding-bottom: 20rpx;
}

.instruction-text {
  font-size: 28rpx;
  color: #333;
  margin-bottom: 30rpx;
  display: block;
}

.qrcode-container {
  display: flex;
  justify-content: center;
  margin: 30rpx 0;
}

.qrcode-image {
  width: 400rpx;
  height: 400rpx;
}

.qrcode-hint {
  font-size: 24rpx;
  color: #999;
  text-align: center;
  display: block;
  margin-bottom: 30rpx;
}

.device-limits {
  background-color: #fff8e1;
  border-radius: 10rpx;
  padding: 20rpx;
}

.limits-title {
  font-size: 28rpx;
  font-weight: 500;
  color: #ff9800;
  margin-bottom: 10rpx;
  display: block;
}

.limits-description {
  font-size: 26rpx;
  color: #666;
}

/* Action Sheet */
.action-sheet {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 999;
}

.action-sheet-content {
  background-color: #fff;
  border-top-left-radius: 20rpx;
  border-top-right-radius: 20rpx;
  overflow: hidden;
}

.action-sheet-title {
  text-align: center;
  font-size: 30rpx;
  color: #999;
  padding: 30rpx 0;
  border-bottom: 1rpx solid #f0f0f0;
}

.action-item {
  text-align: center;
  font-size: 32rpx;
  color: #333;
  padding: 30rpx 0;
  border-bottom: 1rpx solid #f0f0f0;
}

.action-item.warning {
  color: #ff9800;
}

.action-item.danger {
  color: #ff3b30;
}

.action-sheet-cancel {
  background-color: #fff;
  text-align: center;
  font-size: 32rpx;
  color: #333;
  padding: 30rpx 0;
  margin-top: 10rpx;
}
</style> 