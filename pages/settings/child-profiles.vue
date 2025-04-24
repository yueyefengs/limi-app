<template>
  <view class="profiles-container">
    <!-- Header -->
    <view class="header">
      <view class="back-button" @click="navigateBack">
        <image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
      </view>
      <text class="header-title">孩子档案管理</text>
      <view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
    </view>

    <!-- Current Profile -->
    <view class="current-profile-section" v-if="activeProfile">
      <text class="section-title">当前使用的孩子档案</text>
      <view class="profile-card active">
        <view class="profile-avatar">
          <image :src="activeProfile.avatar || defaultAvatar" mode="aspectFill" class="avatar-image"></image>
        </view>
        <view class="profile-info">
          <view class="profile-name-row">
            <text class="profile-name">{{ activeProfile.name }}</text>
            <view class="active-badge">
              <text>当前使用</text>
            </view>
          </view>
          <text class="profile-details">{{ activeProfile.age }}岁 · {{ getGenderText(activeProfile.gender) }}</text>
          <view class="profile-interests">
            <view class="interest-tag" v-for="(interest, idx) in getInterestNames(activeProfile.interests).slice(0, 3)" :key="idx">
              <text>{{ interest }}</text>
            </view>
            <text class="more-interests" v-if="activeProfile.interests.length > 3">+{{ activeProfile.interests.length - 3 }}</text>
          </view>
        </view>
      </view>
    </view>

    <!-- Other Profiles -->
    <view class="other-profiles-section">
      <view class="section-header">
        <text class="section-title">{{ activeProfile ? '其他孩子档案' : '孩子档案列表' }}</text>
        <view class="add-profile-button" @click="navigateToAddProfile">
          <image src="/static/icons/add.png" mode="aspectFit" class="add-icon"></image>
          <text>添加档案</text>
        </view>
      </view>

      <view class="empty-state" v-if="otherProfiles.length === 0">
        <image src="/static/images/empty-profiles.png" mode="aspectFit" class="empty-image"></image>
        <text class="empty-text">还没有添加孩子档案</text>
        <text class="empty-hint">添加孩子档案以个性化内容体验</text>
        <button class="create-profile-button" @click="navigateToAddProfile">创建档案</button>
      </view>

      <view class="profile-list" v-else>
        <view 
          class="profile-card" 
          v-for="profile in otherProfiles" 
          :key="profile.id"
        >
          <view class="profile-avatar">
            <image :src="profile.avatar || defaultAvatar" mode="aspectFill" class="avatar-image"></image>
          </view>
          <view class="profile-info">
            <text class="profile-name">{{ profile.name }}</text>
            <text class="profile-details">{{ profile.age }}岁 · {{ getGenderText(profile.gender) }}</text>
            <view class="profile-interests">
              <view class="interest-tag" v-for="(interest, idx) in getInterestNames(profile.interests).slice(0, 3)" :key="idx">
                <text>{{ interest }}</text>
              </view>
              <text class="more-interests" v-if="profile.interests.length > 3">+{{ profile.interests.length - 3 }}</text>
            </view>
          </view>
          <view class="profile-actions">
            <view class="action-button switch-button" @click="switchProfile(profile)">
              <text>切换</text>
            </view>
            <view class="action-button edit-button" @click="editProfile(profile)">
              <text>编辑</text>
            </view>
          </view>
        </view>
      </view>
    </view>

    <!-- Tutorial Tip -->
    <view class="tutorial-tip" v-if="showTutorial">
      <view class="tip-content">
        <image src="/static/icons/info.png" mode="aspectFit" class="info-icon"></image>
        <view class="tip-text">
          <text class="tip-title">多个孩子可以使用不同档案</text>
          <text class="tip-description">每个档案会根据孩子的年龄和兴趣推荐不同的内容</text>
        </view>
      </view>
      <view class="tip-close" @click="closeTutorial">
        <image src="/static/icons/close.png" mode="aspectFit" class="close-icon"></image>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      profiles: [],
      activeProfileId: null,
      defaultAvatar: '/static/images/default-avatar.png',
      showTutorial: true,
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
      ]
    }
  },
  computed: {
    activeProfile() {
      if (!this.activeProfileId) return null;
      return this.profiles.find(p => p.id === this.activeProfileId);
    },
    otherProfiles() {
      if (!this.activeProfileId) return this.profiles;
      return this.profiles.filter(p => p.id !== this.activeProfileId);
    }
  },
  onShow() {
    this.loadProfiles();
  },
  methods: {
    navigateBack() {
      uni.navigateBack();
    },
    loadProfiles() {
      // Load profiles from storage
      const profiles = uni.getStorageSync('childProfiles') || [];
      this.profiles = profiles;
      
      // Get active profile
      this.activeProfileId = uni.getStorageSync('activeProfileId');
      
      // If no active profile but profiles exist, set the first one as active
      if (!this.activeProfileId && profiles.length > 0) {
        this.activeProfileId = profiles[0].id;
        uni.setStorageSync('activeProfileId', this.activeProfileId);
      }
    },
    navigateToAddProfile() {
      uni.navigateTo({
        url: '/pages/settings/child-profile'
      });
    },
    editProfile(profile) {
      uni.navigateTo({
        url: `/pages/settings/child-profile?id=${profile.id}`
      });
    },
    switchProfile(profile) {
      uni.showLoading({
        title: '正在切换...'
      });
      
      setTimeout(() => {
        this.activeProfileId = profile.id;
        uni.setStorageSync('activeProfileId', profile.id);
        
        uni.hideLoading();
        
        uni.showToast({
          title: `已切换到「${profile.name}」的档案`,
          icon: 'success'
        });
        
        this.loadProfiles(); // Reload to update UI
      }, 800); // Simulate a brief loading period
    },
    getGenderText(gender) {
      return gender === 'male' ? '男孩' : gender === 'female' ? '女孩' : '';
    },
    getInterestNames(interestIds) {
      return interestIds.map(id => {
        const tag = this.interestTags.find(t => t.id === id);
        return tag ? tag.name : '';
      }).filter(name => name !== '');
    },
    closeTutorial() {
      this.showTutorial = false;
      // In a real app, you might want to save this preference
      uni.setStorageSync('profileTutorialSeen', true);
    }
  },
  onLoad() {
    // Check if the tutorial has been seen before
    const tutorialSeen = uni.getStorageSync('profileTutorialSeen');
    if (tutorialSeen) {
      this.showTutorial = false;
    }
  }
}
</script>

<style>
.profiles-container {
  min-height: 100vh;
  background-color: #f5f5f5;
  padding-bottom: env(safe-area-inset-bottom);
  position: relative;
}

.header {
  position: relative;
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

.current-profile-section, .other-profiles-section {
  background-color: #fff;
  margin-top: 20rpx;
  padding: 30rpx 40rpx;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20rpx;
}

.section-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
}

.add-profile-button {
  display: flex;
  align-items: center;
  padding: 10rpx 0;
}

.add-icon {
  width: 32rpx;
  height: 32rpx;
  margin-right: 10rpx;
}

.profile-card {
  display: flex;
  align-items: flex-start;
  padding: 30rpx;
  background-color: #f9f9f9;
  border-radius: 20rpx;
  margin-bottom: 30rpx;
}

.profile-card.active {
  background-color: #e3f2fd;
  border: 2rpx solid #2196f3;
}

.profile-avatar {
  width: 120rpx;
  height: 120rpx;
  border-radius: 60rpx;
  overflow: hidden;
  margin-right: 30rpx;
}

.avatar-image {
  width: 100%;
  height: 100%;
}

.profile-info {
  flex: 1;
}

.profile-name-row {
  display: flex;
  align-items: center;
}

.profile-name {
  font-size: 34rpx;
  font-weight: bold;
  color: #333;
  margin-right: 20rpx;
  max-width: 300rpx;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.active-badge {
  background-color: #2196f3;
  border-radius: 20rpx;
  padding: 4rpx 16rpx;
}

.active-badge text {
  color: white;
  font-size: 24rpx;
}

.profile-details {
  font-size: 28rpx;
  color: #666;
  margin: 10rpx 0;
}

.profile-interests {
  display: flex;
  flex-wrap: wrap;
  margin-top: 10rpx;
}

.interest-tag {
  padding: 8rpx 16rpx;
  background-color: #f0f0f0;
  border-radius: 20rpx;
  margin-right: 16rpx;
  margin-bottom: 10rpx;
}

.profile-card.active .interest-tag {
  background-color: #bbdefb;
}

.interest-tag text {
  font-size: 24rpx;
  color: #666;
}

.more-interests {
  font-size: 24rpx;
  color: #999;
  padding: 8rpx 0;
  margin-left: 10rpx;
}

.profile-actions {
  display: flex;
  flex-direction: column;
  gap: 16rpx;
}

.action-button {
  padding: 10rpx 30rpx;
  border-radius: 30rpx;
  text-align: center;
}

.switch-button {
  background-color: #2196f3;
}

.switch-button text {
  color: white;
  font-size: 28rpx;
}

.edit-button {
  background-color: #f0f0f0;
}

.edit-button text {
  color: #666;
  font-size: 28rpx;
}

.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 60rpx 0;
}

.empty-image {
  width: 240rpx;
  height: 240rpx;
  margin-bottom: 40rpx;
}

.empty-text {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
  margin-bottom: 20rpx;
}

.empty-hint {
  font-size: 28rpx;
  color: #999;
  margin-bottom: 40rpx;
}

.create-profile-button {
  width: 400rpx;
  height: 90rpx;
  line-height: 90rpx;
  background-color: #2196f3;
  color: white;
  font-size: 32rpx;
  border-radius: 45rpx;
}

.tutorial-tip {
  position: fixed;
  bottom: 40rpx;
  left: 40rpx;
  right: 40rpx;
  background-color: #fff;
  border-radius: 20rpx;
  padding: 30rpx;
  box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.1);
  display: flex;
  align-items: flex-start;
  z-index: 10;
}

.tip-content {
  flex: 1;
  display: flex;
  align-items: flex-start;
}

.info-icon {
  width: 48rpx;
  height: 48rpx;
  margin-right: 20rpx;
}

.tip-text {
  flex: 1;
}

.tip-title {
  font-size: 30rpx;
  font-weight: bold;
  color: #333;
  margin-bottom: 10rpx;
  display: block;
}

.tip-description {
  font-size: 28rpx;
  color: #666;
  display: block;
}

.tip-close {
  padding: 10rpx;
}

.close-icon {
  width: 32rpx;
  height: 32rpx;
}
</style> 