<template>
  <view class="profiles-container">
    <!-- Header -->
    <view class="header">
      <view class="back-button" @click="navigateBack">
        <image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
      </view>
      <text class="header-title">角色设定</text>
      <view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
    </view>

    <!-- Current Role Profile -->
    <view class="current-profile-section" v-if="activeProfile">
      <text class="section-title">当前使用的角色</text>
      <view class="profile-card active">
        <view class="profile-avatar">
          <image :src="activeProfile.avatar || defaultAvatar" mode="aspectFill" class="avatar-image"></image>
        </view>
        <view class="profile-info">
          <view class="profile-name-row">
            <text class="profile-name">{{ activeProfile.roleName || activeProfile.name }}</text>
            <view class="active-badge">
              <text>当前使用</text>
            </view>
          </view>
          <text class="profile-details">{{ activeProfile.roleTemplate || "自定义角色" }}</text>
          <view class="profile-interests">
            <view class="interest-tag">
              <text>{{ activeProfile.voiceType || "默认音色" }}</text>
            </view>
            <view class="interest-tag">
              <text>{{ activeProfile.modelType || "默认模型" }}</text>
            </view>
          </view>
        </view>
      </view>
    </view>

    <!-- Other Role Profiles -->
    <view class="other-profiles-section">
      <view class="section-header">
        <text class="section-title">{{ activeProfile ? '其他角色' : '角色列表' }}</text>
        <view class="add-profile-button" @click="navigateToAddProfile">
          <image src="/static/icons/add.png" mode="aspectFit" class="add-icon"></image>
          <text>添加角色</text>
        </view>
      </view>

      <view class="empty-state" v-if="otherProfiles.length === 0">
        <image src="/static/images/empty-profiles.png" mode="aspectFit" class="empty-image"></image>
        <text class="empty-text">还没有添加角色</text>
        <text class="empty-hint">添加角色以定制不同的交互体验</text>
        <button class="create-profile-button" @click="navigateToAddProfile">创建角色</button>
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
            <text class="profile-name">{{ profile.roleName || profile.name }}</text>
            <text class="profile-details">{{ profile.roleTemplate || "自定义角色" }}</text>
            <view class="profile-interests">
              <view class="interest-tag">
                <text>{{ profile.voiceType || "默认音色" }}</text>
              </view>
              <view class="interest-tag">
                <text>{{ profile.modelType || "默认模型" }}</text>
              </view>
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

    <!-- Role Templates Sheet -->
    <uni-popup ref="roleTemplatePopup" type="bottom">
      <view class="popup-content">
        <view class="popup-header">
          <text class="popup-title">选择角色模板</text>
          <view class="popup-close" @click="closeRoleTemplatePopup">
            <text>×</text>
          </view>
        </view>
        <view class="template-list">
          <view 
            class="template-item" 
            v-for="template in roleTemplates" 
            :key="template.id"
            @click="selectRoleTemplate(template)"
          >
            <view class="template-content">
              <text class="template-name">{{ template.name }}</text>
              <text class="template-desc">{{ template.description }}</text>
            </view>
            <radio :checked="currentEditingProfile.roleTemplate === template.id" color="#2196f3" />
          </view>
        </view>
      </view>
    </uni-popup>

    <!-- Voice Type Sheet -->
    <uni-popup ref="voiceTypePopup" type="bottom">
      <view class="popup-content">
        <view class="popup-header">
          <text class="popup-title">选择角色音色</text>
          <view class="popup-close" @click="closeVoiceTypePopup">
            <text>×</text>
          </view>
        </view>
        <view class="voice-list">
          <view 
            class="voice-item" 
            v-for="voice in voiceTypes" 
            :key="voice.id"
            @click="selectVoiceType(voice)"
          >
            <view class="voice-content">
              <text class="voice-name">{{ voice.name }}</text>
              <text class="voice-desc">{{ voice.description }}</text>
            </view>
            <view class="voice-actions">
              <view class="play-button" @click.stop="playVoiceSample(voice)">
                <image src="/static/icons/play.png" mode="aspectFit" class="play-icon"></image>
              </view>
              <radio :checked="currentEditingProfile.voiceType === voice.id" color="#2196f3" />
            </view>
          </view>
        </view>
      </view>
    </uni-popup>

    <!-- Model Selection Sheet -->
    <uni-popup ref="modelSelectionPopup" type="bottom">
      <view class="popup-content">
        <view class="popup-header">
          <text class="popup-title">选择语言模型</text>
          <view class="popup-close" @click="closeModelSelectionPopup">
            <text>×</text>
          </view>
        </view>
        <view class="model-list">
          <view 
            class="model-item" 
            v-for="model in languageModels" 
            :key="model.id"
            @click="selectLanguageModel(model)"
          >
            <view class="model-content">
              <text class="model-name">{{ model.name }}</text>
              <text class="model-desc">{{ model.description }}</text>
            </view>
            <radio :checked="currentEditingProfile.modelType === model.id" color="#2196f3" />
          </view>
        </view>
      </view>
    </uni-popup>

    <!-- Tutorial Tip -->
    <view class="tutorial-tip" v-if="showTutorial">
      <view class="tip-content">
        <image src="/static/icons/info.png" mode="aspectFit" class="info-icon"></image>
        <view class="tip-text">
          <text class="tip-title">不同角色提供不同的交互体验</text>
          <text class="tip-description">您可以根据需求切换不同的角色，体验多样化的交流方式</text>
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
      currentEditingProfile: {},
      roleTemplates: [
        { id: 'taiwan_girl', name: '台湾女友', description: '温柔可爱的台湾女孩，说话甜美亲切' },
        { id: 'tuber', name: '土豆子', description: '幽默风趣的搞笑角色，擅长说段子' },
        { id: 'english_tutor', name: 'English Tutor', description: '专业英语教师，帮助提升英语水平' },
        { id: 'curious_boy', name: '好奇小男孩', description: '充满好奇心的小男孩，喜欢问为什么' },
        { id: 'team_leader', name: '汪汪队队长', description: '负责任的领导者，善于组织和鼓励' }
      ],
      voiceTypes: [
        { id: 'taiwan_girl', name: '台湾小何', description: '柔和甜美的台湾女声', sample: '/static/audio/taiwan_girl.mp3' },
        { id: 'mainland_girl', name: '大陆女声', description: '标准流利的普通话女声', sample: '/static/audio/mainland_girl.mp3' },
        { id: 'boy_voice', name: '男孩声音', description: '活泼阳光的少年音', sample: '/static/audio/boy_voice.mp3' },
        { id: 'robot', name: '机器人', description: '科技感十足的合成音', sample: '/static/audio/robot.mp3' }
      ],
      languageModels: [
        { id: 'gpt4', name: 'GPT-4', description: '能力最强的通用模型，回答全面准确' },
        { id: 'gpt3', name: 'GPT-3.5', description: '平衡速度和质量的通用模型' },
        { id: 'claude', name: 'Claude', description: '擅长自然对话和角色扮演的模型' },
        { id: 'deepseek', name: 'DeepSeek', description: '中文理解能力优秀的国产模型' }
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
      const profiles = uni.getStorageSync('roleProfiles') || [];
      this.profiles = profiles;
      
      // Get active profile
      this.activeProfileId = uni.getStorageSync('activeRoleProfileId');
      
      // If no active profile but profiles exist, set the first one as active
      if (!this.activeProfileId && profiles.length > 0) {
        this.activeProfileId = profiles[0].id;
        uni.setStorageSync('activeRoleProfileId', this.activeProfileId);
      }
    },
    navigateToAddProfile() {
      uni.navigateTo({
        url: '/pages/settings/role-setting'
      });
    },
    editProfile(profile) {
      uni.navigateTo({
        url: `/pages/settings/role-setting?id=${profile.id}`
      });
    },
    switchProfile(profile) {
      uni.showLoading({
        title: '正在切换...'
      });
      
      setTimeout(() => {
        this.activeProfileId = profile.id;
        uni.setStorageSync('activeRoleProfileId', profile.id);
        
        uni.hideLoading();
        
        uni.showToast({
          title: `已切换到「${profile.roleName || profile.name}」`,
          icon: 'success'
        });
        
        this.loadProfiles(); // Reload to update UI
      }, 800); // Simulate a brief loading period
    },
    showRoleTemplatePopup() {
      this.$refs.roleTemplatePopup.open();
    },
    closeRoleTemplatePopup() {
      this.$refs.roleTemplatePopup.close();
    },
    selectRoleTemplate(template) {
      this.currentEditingProfile.roleTemplate = template.id;
      this.closeRoleTemplatePopup();
    },
    showVoiceTypePopup() {
      this.$refs.voiceTypePopup.open();
    },
    closeVoiceTypePopup() {
      this.$refs.voiceTypePopup.close();
    },
    selectVoiceType(voice) {
      this.currentEditingProfile.voiceType = voice.id;
      this.closeVoiceTypePopup();
    },
    playVoiceSample(voice) {
      if (!voice.sample) return;
      
      const innerAudioContext = uni.createInnerAudioContext();
      innerAudioContext.autoplay = true;
      innerAudioContext.src = voice.sample;
      
      innerAudioContext.onError((res) => {
        uni.showToast({
          title: '播放失败',
          icon: 'none'
        });
      });
    },
    showModelSelectionPopup() {
      this.$refs.modelSelectionPopup.open();
    },
    closeModelSelectionPopup() {
      this.$refs.modelSelectionPopup.close();
    },
    selectLanguageModel(model) {
      this.currentEditingProfile.modelType = model.id;
      this.closeModelSelectionPopup();
    },
    closeTutorial() {
      this.showTutorial = false;
      // In a real app, you might want to save this preference
      uni.setStorageSync('roleProfileTutorialSeen', true);
    }
  },
  onLoad() {
    // Check if the tutorial has been seen before
    const tutorialSeen = uni.getStorageSync('roleProfileTutorialSeen');
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

.popup-content {
  background-color: #fff;
  border-radius: 20rpx 20rpx 0 0;
  padding: 30rpx;
  max-height: 70vh;
}

.popup-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30rpx;
}

.popup-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
}

.popup-close {
  padding: 10rpx;
}

.popup-close text {
  font-size: 40rpx;
  color: #999;
}

.template-list, .voice-list, .model-list {
  max-height: 60vh;
  overflow-y: auto;
}

.template-item, .voice-item, .model-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 30rpx 0;
  border-bottom: 1rpx solid #f0f0f0;
}

.template-content, .voice-content, .model-content {
  flex: 1;
}

.template-name, .voice-name, .model-name {
  font-size: 30rpx;
  font-weight: bold;
  color: #333;
  margin-bottom: 10rpx;
}

.template-desc, .voice-desc, .model-desc {
  font-size: 26rpx;
  color: #666;
}

.voice-actions {
  display: flex;
  align-items: center;
}

.play-button {
  width: 60rpx;
  height: 60rpx;
  border-radius: 30rpx;
  background-color: #f0f0f0;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 20rpx;
}

.play-icon {
  width: 32rpx;
  height: 32rpx;
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