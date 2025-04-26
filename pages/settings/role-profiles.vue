<template>
  <view class="role-profiles-container">
    <!-- Header -->
    <view class="header">
      <view class="back-button" @click="navigateBack">
        <image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
      </view>
      <text class="header-title">角色列表</text>
      <view class="add-button" @click="createNewRole">
        <text>+</text>
      </view>
    </view>
    
    <!-- Content -->
    <scroll-view scroll-y class="profiles-content" v-if="profiles.length > 0">
      <view class="profiles-list">
        <view 
          class="profile-item" 
          v-for="profile in profiles" 
          :key="profile.id"
          @click="openProfileDetail(profile.id)"
        >
          <view class="profile-avatar">
            <image 
              :src="profile.avatar || defaultAvatar" 
              mode="aspectFill" 
              class="avatar-image"
            ></image>
          </view>
          
          <view class="profile-info">
            <view class="profile-header">
              <text class="profile-name">{{ profile.roleName }}</text>
              <view class="profile-active" v-if="profile.id === activeProfileId">
                <text>使用中</text>
              </view>
            </view>
            
            <view class="profile-details">
              <text class="template-name">{{ getTemplateName(profile.roleTemplate) }}</text>
              <text class="voice-name">{{ getVoiceName(profile.voiceType) }}</text>
              <text class="model-name">{{ getModelName(profile.modelType) }}</text>
            </view>
          </view>
          
          <view class="profile-actions">
            <view class="action-buttons">
              <view class="edit-button" @click.stop="editProfile(profile.id)">
                <image src="/static/icons/edit.png" mode="aspectFit" class="action-icon"></image>
              </view>
              <view class="delete-button" @click.stop="confirmDelete(profile)">
                <image src="/static/icons/delete.png" mode="aspectFit" class="action-icon"></image>
              </view>
            </view>
            <image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
          </view>
        </view>
      </view>
    </scroll-view>
    
    <!-- Empty State -->
    <view class="empty-state" v-else>
      <image src="/static/icons/empty-roles.png" mode="aspectFit" class="empty-icon"></image>
      <text class="empty-text">还没有创建角色</text>
      <view class="create-button" @click="createNewRole">
        <text>创建角色</text>
      </view>
    </view>
    
    <!-- Delete Confirmation Popup -->
    <uni-popup ref="deleteConfirmPopup" type="dialog">
      <uni-popup-dialog
        type="warn"
        title="删除角色"
        :content="'确定要删除角色\"' + (profileToDelete?.roleName || '') + '\"吗？'"
        :before-close="true"
        @confirm="deleteProfile"
        @close="closeDeletePopup"
      ></uni-popup-dialog>
    </uni-popup>
  </view>
</template>

<script>
export default {
  data() {
    return {
      profiles: [],
      activeProfileId: '',
      profileToDelete: null,
      defaultAvatar: '/static/images/default-avatar.png',
      roleTemplates: [
        { id: 'taiwan_girl', name: '台湾女友' },
        { id: 'tuber', name: '土豆子' },
        { id: 'english_tutor', name: 'English Tutor' },
        { id: 'curious_boy', name: '好奇小男孩' },
        { id: 'team_leader', name: '汪汪队队长' }
      ],
      voiceTypes: [
        { id: 'taiwan_girl', name: '台湾小何' },
        { id: 'mainland_girl', name: '大陆女声' },
        { id: 'boy_voice', name: '男孩声音' },
        { id: 'robot', name: '机器人' }
      ],
      languageModels: [
        { id: 'gpt4', name: 'GPT-4' },
        { id: 'gpt3', name: 'GPT-3.5' },
        { id: 'claude', name: 'Claude' },
        { id: 'deepseek', name: 'DeepSeek' }
      ]
    }
  },
  onShow() {
    // Load profiles when page is shown (to refresh after editing)
    this.loadProfiles();
    this.loadActiveProfile();
  },
  methods: {
    navigateBack() {
      uni.navigateBack();
    },
    loadProfiles() {
      const profiles = uni.getStorageSync('roleProfiles') || [];
      this.profiles = profiles;
    },
    loadActiveProfile() {
      this.activeProfileId = uni.getStorageSync('activeRoleProfileId') || '';
    },
    getTemplateName(templateId) {
      const template = this.roleTemplates.find(t => t.id === templateId);
      return template ? template.name : '未知模板';
    },
    getVoiceName(voiceId) {
      const voice = this.voiceTypes.find(v => v.id === voiceId);
      return voice ? voice.name : '未知音色';
    },
    getModelName(modelId) {
      const model = this.languageModels.find(m => m.id === modelId);
      return model ? model.name : '未知模型';
    },
    createNewRole() {
      uni.navigateTo({
        url: '/pages/settings/role-setting'
      });
    },
    editProfile(id) {
      uni.navigateTo({
        url: `/pages/settings/role-setting?id=${id}`
      });
    },
    openProfileDetail(id) {
      // Set as active profile
      uni.setStorageSync('activeRoleProfileId', id);
      this.activeProfileId = id;
      
      uni.showToast({
        title: '已设为使用中角色',
        icon: 'success'
      });
      
      // After a brief delay, navigate back to previous page
      setTimeout(() => {
        uni.navigateBack();
      }, 1500);
    },
    confirmDelete(profile) {
      this.profileToDelete = profile;
      this.$refs.deleteConfirmPopup.open();
    },
    closeDeletePopup() {
      this.$refs.deleteConfirmPopup.close();
      this.profileToDelete = null;
    },
    deleteProfile() {
      if (!this.profileToDelete) return;
      
      // Cannot delete if it's the only profile
      if (this.profiles.length <= 1) {
        uni.showToast({
          title: '无法删除唯一角色',
          icon: 'none'
        });
        this.closeDeletePopup();
        return;
      }
      
      // Cannot delete if it's the active profile
      if (this.profileToDelete.id === this.activeProfileId) {
        uni.showToast({
          title: '无法删除使用中的角色',
          icon: 'none'
        });
        this.closeDeletePopup();
        return;
      }
      
      // Remove from storage
      const profiles = uni.getStorageSync('roleProfiles') || [];
      const updatedProfiles = profiles.filter(p => p.id !== this.profileToDelete.id);
      uni.setStorageSync('roleProfiles', updatedProfiles);
      
      // Update local data
      this.profiles = updatedProfiles;
      this.closeDeletePopup();
      
      uni.showToast({
        title: '角色已删除',
        icon: 'success'
      });
    }
  }
}
</script>

<style>
.role-profiles-container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  background-color: #f5f5f5;
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

.add-button {
  width: 60rpx;
  height: 60rpx;
  background-color: #2196f3;
  border-radius: 30rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.add-button text {
  color: white;
  font-size: 40rpx;
  font-weight: bold;
  line-height: 1;
}

.profiles-content {
  flex: 1;
  padding: 30rpx;
}

.profiles-list {
  display: flex;
  flex-direction: column;
  gap: 30rpx;
}

.profile-item {
  display: flex;
  align-items: center;
  background-color: #fff;
  border-radius: 20rpx;
  padding: 30rpx;
  box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.03);
}

.profile-avatar {
  width: 100rpx;
  height: 100rpx;
  border-radius: 50rpx;
  overflow: hidden;
  margin-right: 20rpx;
}

.avatar-image {
  width: 100%;
  height: 100%;
}

.profile-info {
  flex: 1;
}

.profile-header {
  display: flex;
  align-items: center;
  margin-bottom: 10rpx;
}

.profile-name {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
  margin-right: 20rpx;
}

.profile-active {
  background-color: #2196f3;
  padding: 6rpx 16rpx;
  border-radius: 20rpx;
}

.profile-active text {
  color: white;
  font-size: 22rpx;
}

.profile-details {
  display: flex;
  flex-wrap: wrap;
  gap: 16rpx;
}

.template-name, .voice-name, .model-name {
  font-size: 24rpx;
  color: #666;
  background-color: #f0f0f0;
  padding: 6rpx 16rpx;
  border-radius: 20rpx;
}

.profile-actions {
  display: flex;
  align-items: center;
}

.action-buttons {
  display: flex;
  gap: 20rpx;
  margin-right: 20rpx;
}

.edit-button, .delete-button {
  width: 50rpx;
  height: 50rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.action-icon {
  width: 40rpx;
  height: 40rpx;
}

.arrow-icon {
  width: 32rpx;
  height: 32rpx;
}

.empty-state {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 60rpx;
}

.empty-icon {
  width: 200rpx;
  height: 200rpx;
  margin-bottom: 40rpx;
}

.empty-text {
  font-size: 32rpx;
  color: #999;
  margin-bottom: 40rpx;
}

.create-button {
  background-color: #2196f3;
  padding: 20rpx 60rpx;
  border-radius: 40rpx;
}

.create-button text {
  color: white;
  font-size: 30rpx;
  font-weight: bold;
}
</style> 