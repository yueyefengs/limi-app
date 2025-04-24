<template>
  <view class="child-profile-container">
    <!-- Header -->
    <view class="header">
      <view class="back-button" @click="navigateBack">
        <image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
      </view>
      <text class="header-title">{{ isEdit ? '编辑孩子档案' : '创建孩子档案' }}</text>
      <view v-if="isEdit" class="delete-button" @click="confirmDelete">
        <image src="/static/icons/delete.png" mode="aspectFit" class="delete-icon"></image>
      </view>
      <view v-else style="width: 40rpx;"></view> <!-- Empty space for alignment -->
    </view>

    <!-- Avatar Selection -->
    <view class="avatar-section">
      <view class="avatar-container" @click="changeAvatar">
        <image :src="profile.avatar || defaultAvatar" mode="aspectFill" class="avatar-image"></image>
        <view class="avatar-edit">
          <image src="/static/icons/camera.png" mode="aspectFit" class="camera-icon"></image>
        </view>
      </view>
      <text class="avatar-hint">点击更换头像</text>
    </view>

    <!-- Profile Form -->
    <view class="form-section">
      <view class="form-item">
        <text class="form-label">孩子名字</text>
        <input 
          type="text" 
          v-model="profile.name" 
          placeholder="请输入孩子的名字" 
          class="form-input"
          maxlength="10"
        />
        <text class="form-hint">{{ profile.name.length }}/10</text>
      </view>

      <view class="form-item">
        <text class="form-label">孩子年龄</text>
        <picker 
          mode="selector" 
          :range="ageOptions" 
          @change="onAgeChange"
          :value="ageIndex"
          class="form-picker"
        >
          <view class="picker-view">
            <text>{{ profile.age === 0 ? '请选择年龄' : profile.age + '岁' }}</text>
            <image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
          </view>
        </picker>
      </view>

      <view class="form-item">
        <text class="form-label">性别</text>
        <view class="gender-selector">
          <view 
            class="gender-option" 
            :class="{ 'selected': profile.gender === 'male' }"
            @click="profile.gender = 'male'"
          >
            <image src="/static/icons/boy.png" mode="aspectFit" class="gender-icon"></image>
            <text>男孩</text>
          </view>
          <view 
            class="gender-option" 
            :class="{ 'selected': profile.gender === 'female' }"
            @click="profile.gender = 'female'"
          >
            <image src="/static/icons/girl.png" mode="aspectFit" class="gender-icon"></image>
            <text>女孩</text>
          </view>
        </view>
      </view>

      <view class="form-item">
        <text class="form-label">学习兴趣</text>
        <view class="interest-tags">
          <view 
            v-for="(tag, index) in interestTags" 
            :key="index"
            class="interest-tag"
            :class="{ 'selected': profile.interests.includes(tag.id) }"
            @click="toggleInterest(tag.id)"
          >
            <text>{{ tag.name }}</text>
          </view>
        </view>
        <text class="form-hint">请选择1-5个兴趣领域</text>
      </view>

      <view class="form-item">
        <text class="form-label">语言偏好</text>
        <picker 
          mode="selector" 
          :range="languageOptions" 
          @change="onLanguageChange"
          :value="languageIndex"
          class="form-picker"
        >
          <view class="picker-view">
            <text>{{ languageOptions[languageIndex] }}</text>
            <image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
          </view>
        </picker>
      </view>

      <view class="form-item">
        <text class="form-label">家长联系方式</text>
        <input 
          type="text" 
          v-model="profile.parentContact" 
          placeholder="请输入手机号码" 
          class="form-input"
        />
      </view>

      <view class="form-item">
        <text class="form-label">学习目标</text>
        <textarea 
          v-model="profile.learningGoals" 
          placeholder="描述您希望孩子通过使用本应用达成的目标（选填）" 
          class="form-textarea"
          maxlength="100"
        />
        <text class="form-hint">{{ profile.learningGoals.length }}/100</text>
      </view>
    </view>

    <!-- Action Buttons -->
    <view class="action-buttons">
      <button class="cancel-button" @click="navigateBack">取消</button>
      <button class="save-button" @click="saveProfile">保存</button>
    </view>

    <!-- Avatar Selector Modal -->
    <view class="modal" v-if="showAvatarSelector">
      <view class="modal-mask" @click="showAvatarSelector = false"></view>
      <view class="modal-content">
        <view class="modal-header">
          <text class="modal-title">选择头像</text>
          <view class="modal-close" @click="showAvatarSelector = false">
            <image src="/static/icons/close.png" mode="aspectFit" class="close-icon"></image>
          </view>
        </view>
        <view class="avatar-grid">
          <view 
            v-for="(avatar, index) in avatarOptions" 
            :key="index"
            class="avatar-option"
            @click="selectAvatar(avatar)"
          >
            <image :src="avatar" mode="aspectFill" class="avatar-option-image"></image>
          </view>
        </view>
        <view class="modal-footer">
          <button class="upload-button" @click="uploadCustomAvatar">
            <image src="/static/icons/upload.png" mode="aspectFit" class="upload-icon"></image>
            <text>上传照片</text>
          </button>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      isEdit: false,
      profileId: null,
      profile: {
        id: '',
        name: '',
        age: 0,
        gender: '',
        avatar: '',
        interests: [],
        language: 'zh-CN',
        parentContact: '',
        learningGoals: ''
      },
      defaultAvatar: '/static/images/default-avatar.png',
      ageOptions: Array.from({ length: 13 }, (_, i) => i + 3), // 3-15岁
      languageOptions: ['中文', '英文', '中英双语'],
      languageValues: ['zh-CN', 'en-US', 'bilingual'],
      interestTags: [
        { id: 'math', name: '数学' },
        { id: 'language', name: '语言' },
        { id: 'science', name: '科学' },
        { id: 'art', name: '艺术' },
        { id: 'music', name: '音乐' },
        { id: 'nature', name: '自然' },
        { id: 'history', name: '历史' },
        { id: 'sports', name: '体育' },
        { id: 'reading', name: '阅读' },
        { id: 'coding', name: '编程' }
      ],
      showAvatarSelector: false,
      avatarOptions: [
        '/static/images/avatars/avatar1.png',
        '/static/images/avatars/avatar2.png',
        '/static/images/avatars/avatar3.png',
        '/static/images/avatars/avatar4.png',
        '/static/images/avatars/avatar5.png',
        '/static/images/avatars/avatar6.png',
        '/static/images/avatars/avatar7.png',
        '/static/images/avatars/avatar8.png'
      ]
    }
  },
  computed: {
    ageIndex() {
      return this.ageOptions.indexOf(this.profile.age) !== -1 
        ? this.ageOptions.indexOf(this.profile.age) 
        : 0;
    },
    languageIndex() {
      const index = this.languageValues.indexOf(this.profile.language);
      return index !== -1 ? index : 0;
    }
  },
  onLoad(options) {
    if (options.id) {
      this.isEdit = true;
      this.profileId = options.id;
      this.loadProfile(options.id);
    } else {
      // Generate a new ID for new profile
      this.profile.id = this.generateProfileId();
    }
  },
  methods: {
    navigateBack() {
      uni.navigateBack();
    },
    generateProfileId() {
      return 'profile_' + Date.now();
    },
    loadProfile(id) {
      // Load profile from storage
      const profiles = uni.getStorageSync('childProfiles') || [];
      const profile = profiles.find(p => p.id === id);
      
      if (profile) {
        this.profile = JSON.parse(JSON.stringify(profile)); // Clone to avoid direct reference
      } else {
        uni.showToast({
          title: '未找到孩子资料',
          icon: 'none'
        });
        setTimeout(() => {
          uni.navigateBack();
        }, 1500);
      }
    },
    onAgeChange(e) {
      const index = e.detail.value;
      this.profile.age = this.ageOptions[index];
    },
    onLanguageChange(e) {
      const index = e.detail.value;
      this.profile.language = this.languageValues[index];
    },
    toggleInterest(interestId) {
      const index = this.profile.interests.indexOf(interestId);
      
      if (index !== -1) {
        // Remove interest
        this.profile.interests.splice(index, 1);
      } else {
        // Add interest if less than 5
        if (this.profile.interests.length < 5) {
          this.profile.interests.push(interestId);
        } else {
          uni.showToast({
            title: '最多选择5个兴趣',
            icon: 'none'
          });
        }
      }
    },
    changeAvatar() {
      this.showAvatarSelector = true;
    },
    selectAvatar(avatar) {
      this.profile.avatar = avatar;
      this.showAvatarSelector = false;
    },
    uploadCustomAvatar() {
      uni.chooseImage({
        count: 1,
        sizeType: ['compressed'],
        sourceType: ['album', 'camera'],
        success: (res) => {
          // In a real app, you would upload the image to a server
          // Here we'll just use the temporary path directly
          this.profile.avatar = res.tempFilePaths[0];
          this.showAvatarSelector = false;
        }
      });
    },
    validateProfile() {
      if (!this.profile.name) {
        uni.showToast({
          title: '请输入孩子的名字',
          icon: 'none'
        });
        return false;
      }
      
      if (!this.profile.age) {
        uni.showToast({
          title: '请选择孩子的年龄',
          icon: 'none'
        });
        return false;
      }
      
      if (!this.profile.gender) {
        uni.showToast({
          title: '请选择孩子的性别',
          icon: 'none'
        });
        return false;
      }
      
      if (this.profile.interests.length === 0) {
        uni.showToast({
          title: '请至少选择一个兴趣',
          icon: 'none'
        });
        return false;
      }
      
      return true;
    },
    saveProfile() {
      if (!this.validateProfile()) {
        return;
      }
      
      const profiles = uni.getStorageSync('childProfiles') || [];
      
      if (this.isEdit) {
        // Update existing profile
        const index = profiles.findIndex(p => p.id === this.profileId);
        if (index !== -1) {
          profiles[index] = this.profile;
        } else {
          profiles.push(this.profile);
        }
      } else {
        // Add new profile
        profiles.push(this.profile);
      }
      
      uni.setStorageSync('childProfiles', profiles);
      
      uni.showToast({
        title: this.isEdit ? '档案已更新' : '档案已创建',
        icon: 'success'
      });
      
      setTimeout(() => {
        uni.navigateBack();
      }, 1500);
    },
    confirmDelete() {
      uni.showModal({
        title: '确认删除',
        content: `确定要删除「${this.profile.name}」的档案吗？此操作不可恢复。`,
        confirmText: '删除',
        confirmColor: '#FF0000',
        success: (res) => {
          if (res.confirm) {
            this.deleteProfile();
          }
        }
      });
    },
    deleteProfile() {
      let profiles = uni.getStorageSync('childProfiles') || [];
      profiles = profiles.filter(p => p.id !== this.profileId);
      uni.setStorageSync('childProfiles', profiles);
      
      uni.showToast({
        title: '档案已删除',
        icon: 'success'
      });
      
      setTimeout(() => {
        uni.navigateBack();
      }, 1500);
    }
  }
}
</script>

<style>
.child-profile-container {
  min-height: 100vh;
  background-color: #f5f5f5;
  padding-bottom: 40rpx;
}

.header {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 80rpx 40rpx 20rpx;
  background-color: #fff;
}

.back-button, .delete-button {
  padding: 10rpx;
}

.back-icon, .delete-icon {
  width: 40rpx;
  height: 40rpx;
}

.header-title {
  font-size: 36rpx;
  font-weight: bold;
  color: #333;
}

.avatar-section {
  background-color: #fff;
  padding: 40rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  border-bottom: 1px solid #eee;
}

.avatar-container {
  position: relative;
  width: 200rpx;
  height: 200rpx;
  border-radius: 100rpx;
  overflow: hidden;
  margin-bottom: 20rpx;
}

.avatar-image {
  width: 100%;
  height: 100%;
}

.avatar-edit {
  position: absolute;
  bottom: 0;
  right: 0;
  width: 60rpx;
  height: 60rpx;
  background-color: #2196f3;
  border-radius: 30rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.camera-icon {
  width: 32rpx;
  height: 32rpx;
}

.avatar-hint {
  font-size: 28rpx;
  color: #999;
}

.form-section {
  background-color: #fff;
  margin-top: 20rpx;
  padding: 0 40rpx;
}

.form-item {
  padding: 30rpx 0;
  border-bottom: 1px solid #eee;
}

.form-item:last-child {
  border-bottom: none;
}

.form-label {
  font-size: 32rpx;
  color: #333;
  margin-bottom: 20rpx;
  display: block;
}

.form-input {
  height: 80rpx;
  background-color: #f5f5f5;
  padding: 0 20rpx;
  border-radius: 10rpx;
  font-size: 30rpx;
}

.form-textarea {
  width: 100%;
  height: 160rpx;
  background-color: #f5f5f5;
  padding: 20rpx;
  border-radius: 10rpx;
  font-size: 30rpx;
  box-sizing: border-box;
}

.form-hint {
  font-size: 24rpx;
  color: #999;
  margin-top: 10rpx;
  text-align: right;
}

.picker-view {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 80rpx;
  background-color: #f5f5f5;
  padding: 0 20rpx;
  border-radius: 10rpx;
  font-size: 30rpx;
}

.arrow-icon {
  width: 32rpx;
  height: 32rpx;
}

.gender-selector {
  display: flex;
  gap: 40rpx;
  margin-top: 20rpx;
}

.gender-option {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 30rpx 0;
  background-color: #f5f5f5;
  border-radius: 10rpx;
}

.gender-option.selected {
  background-color: #e3f2fd;
  border: 2rpx solid #2196f3;
}

.gender-icon {
  width: 80rpx;
  height: 80rpx;
  margin-bottom: 10rpx;
}

.interest-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 20rpx;
  margin: 20rpx 0;
}

.interest-tag {
  padding: 15rpx 30rpx;
  border-radius: 30rpx;
  background-color: #f0f0f0;
  font-size: 28rpx;
  color: #666;
}

.interest-tag.selected {
  background-color: #e3f2fd;
  color: #2196f3;
}

.action-buttons {
  display: flex;
  justify-content: space-between;
  padding: 40rpx;
  gap: 30rpx;
}

.cancel-button, .save-button {
  flex: 1;
  height: 90rpx;
  line-height: 90rpx;
  font-size: 32rpx;
  border-radius: 45rpx;
}

.cancel-button {
  background-color: #f5f5f5;
  color: #666;
}

.save-button {
  background-color: #2196f3;
  color: white;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 1000;
}

.modal-mask {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-content {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: #fff;
  border-top-left-radius: 20rpx;
  border-top-right-radius: 20rpx;
  overflow: hidden;
  padding-bottom: env(safe-area-inset-bottom);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 30rpx;
  border-bottom: 1px solid #eee;
}

.modal-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
}

.close-icon {
  width: 40rpx;
  height: 40rpx;
}

.avatar-grid {
  display: flex;
  flex-wrap: wrap;
  padding: 30rpx;
}

.avatar-option {
  width: 25%;
  padding: 20rpx;
  box-sizing: border-box;
}

.avatar-option-image {
  width: 100%;
  height: 0;
  padding-bottom: 100%;
  border-radius: 50%;
}

.modal-footer {
  padding: 20rpx 30rpx 40rpx;
}

.upload-button {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 90rpx;
  background-color: #f5f5f5;
  border-radius: 45rpx;
  font-size: 30rpx;
  color: #333;
}

.upload-icon {
  width: 32rpx;
  height: 32rpx;
  margin-right: 10rpx;
}
</style> 