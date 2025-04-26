<template>
  <view class="role-setting-container">
    <!-- Header -->
    <view class="header">
      <view class="header-left">
        <view class="back-button" @click="handleBackClick">
          <image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
        </view>
        <text class="header-title">{{ isEdit ? '编辑角色' : '新建角色' }}</text>
      </view>
      <view class="save-button" @click="saveProfile">
        <text>保存</text>
      </view>
    </view>
    
    <!-- Content -->
    <scroll-view scroll-y class="settings-content">
      <!-- Role Templates -->
      <view class="settings-section">
        <view class="section-header">
          <text class="section-title">角色模板</text>
          <text class="section-description">选择一个预设角色模板，或者从零开始创建</text>
        </view>
        
        <view class="templates-grid">
          <view 
            class="template-item" 
            v-for="template in roleTemplates" 
            :key="template.id"
            :class="{ active: selectedTemplate === template.id }"
            @click="selectTemplate(template.id)"
          >
            <image :src="template.image || defaultTemplateImage" mode="aspectFill" class="template-image"></image>
            <text class="template-name">{{ template.name }}</text>
          </view>
        </view>
      </view>
      
      <!-- Role Nickname -->
      <view class="settings-section">
        <view class="section-header">
          <text class="section-title">角色昵称</text>
          <text class="section-description">给你的角色起个好听的名字吧</text>
        </view>
        
        <view class="input-container">
          <input 
            type="text" 
            v-model="roleName" 
            placeholder="请输入角色昵称" 
            class="role-input"
            maxlength="20"
          />
          <text class="input-count">{{ roleName.length }}/20</text>
        </view>
      </view>
      
      <!-- Voice Type -->
      <view class="settings-section">
        <view class="section-header">
          <text class="section-title">角色音色</text>
          <text class="section-description">选择一个合适的音色让对话更生动</text>
        </view>
        
        <view class="select-container" @click="openVoicePopup">
          <text class="selected-value">{{ getVoiceName(selectedVoice) }}</text>
          <image src="/static/icons/arrow-down.png" mode="aspectFit" class="arrow-icon"></image>
        </view>
      </view>
      
      <!-- Role Description -->
      <view class="settings-section">
        <view class="section-header">
          <text class="section-title">角色介绍</text>
          <text class="section-description">详细描述角色的性格、特点和背景</text>
        </view>
        
        <view class="textarea-container">
          <textarea 
            v-model="roleDescription" 
            placeholder="请输入角色介绍" 
            class="role-textarea"
            maxlength="1000"
          />
          <text class="input-count">{{ roleDescription.length }}/1000</text>
        </view>
      </view>
      
      <!-- Language Model -->
      <view class="settings-section">
        <view class="section-header">
          <text class="section-title">语言模型</text>
          <text class="section-description">选择一个适合你的需求的语言模型</text>
        </view>
        
        <view class="select-container" @click="openModelPopup">
          <text class="selected-value">{{ getModelName(selectedModel) }}</text>
          <image src="/static/icons/arrow-down.png" mode="aspectFit" class="arrow-icon"></image>
        </view>
      </view>
    </scroll-view>
    
    <!-- Voice Type Popup -->
    <uni-popup ref="voicePopup" type="bottom">
      <view class="popup-content">
        <view class="popup-header">
          <text class="popup-title">选择角色音色</text>
          <view class="popup-close" @click="closeVoicePopup">
            <image src="/static/icons/close.png" mode="aspectFit" class="close-icon"></image>
          </view>
        </view>
        
        <view class="popup-options">
          <view 
            class="popup-option" 
            v-for="voice in voiceTypes" 
            :key="voice.id"
            :class="{ active: selectedVoice === voice.id }"
            @click="selectVoice(voice.id)"
          >
            <text class="option-name">{{ voice.name }}</text>
            <image 
              v-if="selectedVoice === voice.id" 
              src="/static/icons/check.png" 
              mode="aspectFit" 
              class="check-icon"
            ></image>
          </view>
        </view>
      </view>
    </uni-popup>
    
    <!-- Language Model Popup -->
    <uni-popup ref="modelPopup" type="bottom">
      <view class="popup-content">
        <view class="popup-header">
          <text class="popup-title">选择语言模型</text>
          <view class="popup-close" @click="closeModelPopup">
            <image src="/static/icons/close.png" mode="aspectFit" class="close-icon"></image>
          </view>
        </view>
        
        <view class="popup-options">
          <view 
            class="popup-option" 
            v-for="model in languageModels" 
            :key="model.id"
            :class="{ active: selectedModel === model.id }"
            @click="selectModel(model.id)"
          >
            <text class="option-name">{{ model.name }}</text>
            <image 
              v-if="selectedModel === model.id" 
              src="/static/icons/check.png" 
              mode="aspectFit" 
              class="check-icon"
            ></image>
          </view>
        </view>
      </view>
    </uni-popup>
    
    <!-- Discard Changes Dialog -->
    <uni-popup ref="discardPopup" type="dialog">
      <uni-popup-dialog
        type="warn"
        title="丢弃修改"
        content="您有未保存的更改，确定要离开吗？"
        :before-close="true"
        @confirm="confirmDiscard"
        @close="closeDiscardPopup"
      ></uni-popup-dialog>
    </uni-popup>
  </view>
</template>

<script>
export default {
  data() {
    return {
      isEdit: false,
      profileId: '',
      originalData: null,
      
      // Form data
      roleName: '',
      selectedTemplate: '',
      selectedVoice: '',
      roleDescription: '',
      selectedModel: '',
      
      // Data lists
      roleTemplates: [
        { id: 'taiwan_girl', name: '台湾女友', image: '/static/images/templates/taiwan_girl.png' },
        { id: 'tuber', name: '土豆子', image: '/static/images/templates/tuber.png' },
        { id: 'english_tutor', name: 'English Tutor', image: '/static/images/templates/english_tutor.png' },
        { id: 'curious_boy', name: '好奇小男孩', image: '/static/images/templates/curious_boy.png' },
        { id: 'team_leader', name: '汪汪队队长', image: '/static/images/templates/team_leader.png' }
      ],
      voiceTypes: [
        { id: 'taiwan_girl', name: '台湾小何' },
        { id: 'mainland_girl', name: '大陆女声' },
        { id: 'boy_voice', name: '男孩声音' },
        { id: 'robot', name: '机器人' }
      ],
      languageModels: [
        { id: 'gpt4', name: 'GPT-4', description: '最强大的模型，适合复杂的对话' },
        { id: 'gpt3', name: 'GPT-3.5', description: '快速且经济的选择' },
        { id: 'claude', name: 'Claude', description: '擅长长对话和复杂问题' },
        { id: 'deepseek', name: 'DeepSeek', description: '适合中文对话' }
      ],
      
      // Defaults
      defaultTemplateImage: '/static/images/templates/default.png',
      hasChanges: false
    }
  },
  onLoad(options) {
    // Check if we're editing an existing profile
    if (options.id) {
      this.isEdit = true;
      this.profileId = options.id;
      this.loadProfile(options.id);
    } else {
      // Set defaults for new profile
      this.selectedVoice = this.voiceTypes[0].id;
      this.selectedModel = this.languageModels[0].id;
    }
  },
  computed: {
    // Check if form is valid
    isFormValid() {
      return this.roleName.trim() !== '' && this.selectedVoice !== '' && this.selectedModel !== '';
    },
    // Check if there are unsaved changes
    hasUnsavedChanges() {
      if (!this.isEdit) {
        // For new profiles, check if any data has been entered
        return this.roleName !== '' || this.selectedTemplate !== '' || 
               this.roleDescription !== '' || this.hasChanges;
      } else if (this.originalData) {
        // For existing profiles, compare with original data
        return this.roleName !== this.originalData.roleName ||
               this.selectedTemplate !== this.originalData.roleTemplate ||
               this.selectedVoice !== this.originalData.voiceType ||
               this.roleDescription !== this.originalData.description ||
               this.selectedModel !== this.originalData.modelType ||
               this.hasChanges;
      }
      return false;
    }
  },
  methods: {
    // Navigation handling
    handleBackClick() {
      if (this.hasUnsavedChanges) {
        this.$refs.discardPopup.open();
      } else {
        this.navigateBack();
      }
    },
    navigateBack() {
      uni.navigateBack();
    },
    confirmDiscard() {
      this.$refs.discardPopup.close();
      this.navigateBack();
    },
    closeDiscardPopup() {
      this.$refs.discardPopup.close();
    },
    
    // Template selection
    selectTemplate(templateId) {
      this.selectedTemplate = templateId;
      this.hasChanges = true;
      
      // Load template data
      const template = this.roleTemplates.find(t => t.id === templateId);
      if (template) {
        // Here you would normally load the template details from an API
        // For now, we'll just set some dummy data based on the template
        switch(templateId) {
          case 'taiwan_girl':
            this.roleName = this.roleName || '小何';
            this.selectedVoice = 'taiwan_girl';
            this.roleDescription = '你是一个来自台湾的女孩，性格活泼开朗，喜欢用台湾当地的用语和表达方式交流。你对美食很热爱，尤其喜欢夜市小吃。说话时会带有一些台湾口音和用语，如"蛮好的啦"、"真的假的"等。';
            break;
          case 'tuber':
            this.roleName = this.roleName || '土豆子';
            this.selectedVoice = 'boy_voice';
            this.roleDescription = '你是一个活泼可爱的小男孩，对世界充满好奇。说话方式俏皮可爱，常常会问很多"为什么"。你有着丰富的想象力，喜欢讲故事，特别是关于宇宙、恐龙和超级英雄的话题。';
            break;
          case 'english_tutor':
            this.roleName = this.roleName || 'Teacher Mike';
            this.selectedVoice = 'robot';
            this.roleDescription = '你是一位经验丰富的英语教师，擅长教授英语口语和写作。你会以友好且鼓励的方式与学生交流，耐心纠正语法错误，提供地道的英语表达。你会根据学生的语言水平调整教学内容，从基础对话到高级辩论都能胜任。';
            break;
          // Add other templates as needed
        }
      }
    },
    
    // Voice selection
    openVoicePopup() {
      this.$refs.voicePopup.open();
    },
    closeVoicePopup() {
      this.$refs.voicePopup.close();
    },
    selectVoice(voiceId) {
      this.selectedVoice = voiceId;
      this.hasChanges = true;
      this.closeVoicePopup();
    },
    getVoiceName(voiceId) {
      const voice = this.voiceTypes.find(v => v.id === voiceId);
      return voice ? voice.name : '请选择音色';
    },
    
    // Model selection
    openModelPopup() {
      this.$refs.modelPopup.open();
    },
    closeModelPopup() {
      this.$refs.modelPopup.close();
    },
    selectModel(modelId) {
      this.selectedModel = modelId;
      this.hasChanges = true;
      this.closeModelPopup();
    },
    getModelName(modelId) {
      const model = this.languageModels.find(m => m.id === modelId);
      return model ? model.name : '请选择语言模型';
    },
    
    // Load existing profile
    loadProfile(profileId) {
      const profiles = uni.getStorageSync('roleProfiles') || [];
      const profile = profiles.find(p => p.id === profileId);
      
      if (profile) {
        this.roleName = profile.roleName;
        this.selectedTemplate = profile.roleTemplate || '';
        this.selectedVoice = profile.voiceType;
        this.roleDescription = profile.description || '';
        this.selectedModel = profile.modelType;
        
        // Store original data for change detection
        this.originalData = { ...profile };
      } else {
        uni.showToast({
          title: '未找到角色信息',
          icon: 'none'
        });
        setTimeout(() => {
          this.navigateBack();
        }, 1500);
      }
    },
    
    // Save profile
    saveProfile() {
      if (!this.isFormValid) {
        uni.showToast({
          title: '请填写必要信息',
          icon: 'none'
        });
        return;
      }
      
      const profileData = {
        roleName: this.roleName,
        roleTemplate: this.selectedTemplate,
        voiceType: this.selectedVoice,
        description: this.roleDescription,
        modelType: this.selectedModel,
        createdAt: new Date().getTime()
      };
      
      const profiles = uni.getStorageSync('roleProfiles') || [];
      
      if (this.isEdit) {
        // Update existing profile
        profileData.id = this.profileId;
        profileData.updatedAt = new Date().getTime();
        
        const index = profiles.findIndex(p => p.id === this.profileId);
        if (index !== -1) {
          profiles[index] = { ...profiles[index], ...profileData };
        } else {
          profiles.push(profileData);
        }
      } else {
        // Create new profile
        profileData.id = 'role_' + new Date().getTime();
        profiles.push(profileData);
        
        // If this is the first profile, set it as active
        if (profiles.length === 1) {
          uni.setStorageSync('activeRoleProfileId', profileData.id);
        }
      }
      
      uni.setStorageSync('roleProfiles', profiles);
      
      uni.showToast({
        title: this.isEdit ? '角色已更新' : '角色已创建',
        icon: 'success'
      });
      
      setTimeout(() => {
        this.navigateBack();
      }, 1500);
    }
  }
}
</script>

<style>
.role-setting-container {
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

.header-left {
  display: flex;
  align-items: center;
}

.back-button {
  padding: 10rpx;
  margin-right: 20rpx;
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

.save-button {
  background-color: #2196f3;
  padding: 15rpx 40rpx;
  border-radius: 40rpx;
}

.save-button text {
  color: white;
  font-size: 28rpx;
  font-weight: bold;
}

.settings-content {
  flex: 1;
  padding: 30rpx;
}

.settings-section {
  background-color: #fff;
  border-radius: 20rpx;
  padding: 30rpx;
  margin-bottom: 30rpx;
}

.section-header {
  margin-bottom: 20rpx;
}

.section-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
  margin-bottom: 10rpx;
  display: block;
}

.section-description {
  font-size: 24rpx;
  color: #999;
  display: block;
}

.templates-grid {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  margin: 0 -10rpx;
}

.template-item {
  width: calc(33.33% - 20rpx);
  margin: 10rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20rpx;
  border-radius: 15rpx;
  border: 2rpx solid #eee;
}

.template-item.active {
  border-color: #2196f3;
  background-color: rgba(33, 150, 243, 0.05);
}

.template-image {
  width: 120rpx;
  height: 120rpx;
  border-radius: 60rpx;
  margin-bottom: 15rpx;
}

.template-name {
  font-size: 24rpx;
  color: #333;
  text-align: center;
}

.input-container {
  position: relative;
  margin-bottom: 10rpx;
}

.role-input {
  background-color: #f7f7f7;
  border-radius: 10rpx;
  padding: 20rpx;
  font-size: 28rpx;
  width: 100%;
  box-sizing: border-box;
}

.input-count {
  position: absolute;
  right: 20rpx;
  bottom: 20rpx;
  font-size: 22rpx;
  color: #999;
}

.select-container {
  background-color: #f7f7f7;
  border-radius: 10rpx;
  padding: 20rpx;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.selected-value {
  font-size: 28rpx;
  color: #333;
}

.arrow-icon {
  width: 32rpx;
  height: 32rpx;
}

.textarea-container {
  position: relative;
}

.role-textarea {
  background-color: #f7f7f7;
  border-radius: 10rpx;
  padding: 20rpx;
  font-size: 28rpx;
  width: 100%;
  height: 300rpx;
  box-sizing: border-box;
}

.popup-content {
  background-color: #fff;
  border-top-left-radius: 20rpx;
  border-top-right-radius: 20rpx;
  padding-bottom: 50rpx;
}

.popup-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 30rpx;
  border-bottom: 1rpx solid #eee;
}

.popup-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
}

.popup-close {
  padding: 10rpx;
}

.close-icon {
  width: 32rpx;
  height: 32rpx;
}

.popup-options {
  max-height: 600rpx;
  overflow-y: auto;
}

.popup-option {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 30rpx;
  border-bottom: 1rpx solid #f5f5f5;
}

.popup-option.active {
  background-color: rgba(33, 150, 243, 0.05);
}

.option-name {
  font-size: 28rpx;
  color: #333;
}

.check-icon {
  width: 40rpx;
  height: 40rpx;
}
</style> 