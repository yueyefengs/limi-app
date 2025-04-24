<template>
  <view class="register-container">
    <!-- Header -->
    <view class="register-header">
      <view class="back-button" @tap="navigateBack">
        <text class="back-icon">←</text>
      </view>
      <text class="page-title">注册账号</text>
      <view class="placeholder"></view>
    </view>

    <!-- Registration Form -->
    <view class="register-form">
      <view class="form-group">
        <text class="form-label">手机号码</text>
        <view class="input-container">
          <view class="country-code" @tap="showCountryCodePicker">
            <text>+{{ countryCode }}</text>
            <text class="down-arrow">▼</text>
          </view>
          <input 
            type="number" 
            class="phone-input" 
            placeholder="请输入手机号码" 
            v-model="phone"
            maxlength="11"
            @input="validatePhone"
          />
        </view>
        <text class="error-message" v-if="errors.phone">{{ errors.phone }}</text>
      </view>

      <view class="form-group">
        <text class="form-label">验证码</text>
        <view class="input-container verification">
          <input 
            type="number" 
            class="code-input" 
            placeholder="请输入验证码" 
            v-model="verificationCode"
            maxlength="6"
            @input="validateVerificationCode"
          />
          <view 
            class="get-code-button" 
            :class="{ 'disabled': !isPhoneValid || cooldown > 0 }"
            @tap="getVerificationCode"
          >
            <text>{{ cooldown > 0 ? `${cooldown}秒后重发` : '获取验证码' }}</text>
          </view>
        </view>
        <text class="error-message" v-if="errors.verificationCode">{{ errors.verificationCode }}</text>
      </view>

      <view class="form-group">
        <text class="form-label">设置密码</text>
        <view class="input-container">
          <input 
            :type="showPassword ? 'text' : 'password'" 
            class="password-input" 
            placeholder="请设置6-20位密码" 
            v-model="password"
            @input="validatePassword"
          />
          <view class="toggle-visibility" @tap="togglePasswordVisibility">
            <text>{{ showPassword ? '👁️' : '👁️‍🗨️' }}</text>
          </view>
        </view>
        <text class="error-message" v-if="errors.password">{{ errors.password }}</text>
      </view>

      <view class="form-group">
        <text class="form-label">确认密码</text>
        <view class="input-container">
          <input 
            :type="showConfirmPassword ? 'text' : 'password'" 
            class="password-input" 
            placeholder="请再次输入密码" 
            v-model="confirmPassword"
            @input="validateConfirmPassword"
          />
          <view class="toggle-visibility" @tap="toggleConfirmPasswordVisibility">
            <text>{{ showConfirmPassword ? '👁️' : '👁️‍🗨️' }}</text>
          </view>
        </view>
        <text class="error-message" v-if="errors.confirmPassword">{{ errors.confirmPassword }}</text>
      </view>

      <!-- Agreement Checkbox -->
      <view class="agreement-container">
        <view class="checkbox" :class="{ 'checked': agreeToTerms }" @tap="toggleAgreement">
          <view class="checkbox-inner" v-if="agreeToTerms"></view>
        </view>
        <view class="agreement-text">
          <text>我已阅读并同意</text>
          <text class="link" @tap="navigateToUserAgreement">《用户协议》</text>
          <text>和</text>
          <text class="link" @tap="navigateToPrivacyPolicy">《隐私政策》</text>
        </view>
      </view>
      <text class="error-message agreement-error" v-if="errors.agreement">{{ errors.agreement }}</text>

      <!-- Register Button -->
      <button class="register-button" @tap="register">注册</button>

      <!-- Login Link -->
      <view class="login-link-container">
        <text>已有账号？</text>
        <text class="login-link" @tap="navigateToLogin">立即登录</text>
      </view>
    </view>

    <!-- Country Code Picker (modal) -->
    <view class="modal-overlay" v-if="showCountryCodeModal" @tap="hideCountryCodePicker">
      <view class="modal-content" @tap.stop>
        <view class="modal-header">
          <text class="modal-title">选择国家/地区</text>
          <text class="modal-close" @tap="hideCountryCodePicker">×</text>
        </view>
        <scroll-view class="country-list" scroll-y>
          <view 
            class="country-item" 
            v-for="(country, index) in countryCodes" 
            :key="index"
            @tap="selectCountryCode(country.code)"
          >
            <text class="country-name">{{ country.name }}</text>
            <text class="country-code-value">+{{ country.code }}</text>
          </view>
        </scroll-view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      phone: '',
      verificationCode: '',
      password: '',
      confirmPassword: '',
      countryCode: '86',
      showPassword: false,
      showConfirmPassword: false,
      agreeToTerms: false,
      cooldown: 0,
      cooldownInterval: null,
      showCountryCodeModal: false,
      isPhoneValid: false,
      errors: {
        phone: '',
        verificationCode: '',
        password: '',
        confirmPassword: '',
        agreement: ''
      },
      // List of country codes
      countryCodes: [
        { name: '中国', code: '86' },
        { name: '香港', code: '852' },
        { name: '澳门', code: '853' },
        { name: '台湾', code: '886' },
        { name: '美国', code: '1' },
        { name: '日本', code: '81' },
        { name: '韩国', code: '82' },
        { name: '英国', code: '44' },
        { name: '澳大利亚', code: '61' },
        { name: '新加坡', code: '65' }
      ]
    }
  },
  onUnload() {
    // Clear interval when page is unloaded
    if (this.cooldownInterval) {
      clearInterval(this.cooldownInterval);
    }
  },
  methods: {
    navigateBack() {
      uni.navigateBack();
    },
    
    validatePhone() {
      // Clear previous error
      this.errors.phone = '';
      
      if (!this.phone) {
        this.errors.phone = '请输入手机号码';
        this.isPhoneValid = false;
        return;
      }
      
      // Basic phone validation for China (11 digits)
      if (this.countryCode === '86' && !/^1\d{10}$/.test(this.phone)) {
        this.errors.phone = '请输入正确的手机号码';
        this.isPhoneValid = false;
        return;
      }
      
      this.isPhoneValid = true;
    },
    
    validateVerificationCode() {
      this.errors.verificationCode = '';
      
      if (!this.verificationCode) {
        this.errors.verificationCode = '请输入验证码';
        return;
      }
      
      if (!/^\d{6}$/.test(this.verificationCode)) {
        this.errors.verificationCode = '验证码应为6位数字';
        return;
      }
    },
    
    validatePassword() {
      this.errors.password = '';
      
      if (!this.password) {
        this.errors.password = '请设置密码';
        return;
      }
      
      if (this.password.length < 6 || this.password.length > 20) {
        this.errors.password = '密码长度应为6-20位';
        return;
      }
      
      // Check if password contains at least one letter and one number
      if (!/^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{6,20}$/.test(this.password)) {
        this.errors.password = '密码应包含字母和数字';
        return;
      }
      
      // If confirm password is entered, validate it
      if (this.confirmPassword) {
        this.validateConfirmPassword();
      }
    },
    
    validateConfirmPassword() {
      this.errors.confirmPassword = '';
      
      if (!this.confirmPassword) {
        this.errors.confirmPassword = '请再次输入密码';
        return;
      }
      
      if (this.confirmPassword !== this.password) {
        this.errors.confirmPassword = '两次输入的密码不一致';
        return;
      }
    },
    
    togglePasswordVisibility() {
      this.showPassword = !this.showPassword;
    },
    
    toggleConfirmPasswordVisibility() {
      this.showConfirmPassword = !this.showConfirmPassword;
    },
    
    toggleAgreement() {
      this.agreeToTerms = !this.agreeToTerms;
      if (this.agreeToTerms) {
        this.errors.agreement = '';
      }
    },
    
    getVerificationCode() {
      if (!this.isPhoneValid || this.cooldown > 0) {
        return;
      }
      
      // Start cooldown
      this.cooldown = 60;
      this.cooldownInterval = setInterval(() => {
        this.cooldown--;
        if (this.cooldown <= 0) {
          clearInterval(this.cooldownInterval);
        }
      }, 1000);
      
      // In a real app, this would make an API call to send the verification code
      uni.showToast({
        title: '验证码已发送',
        icon: 'none'
      });
      
      // For demo purposes, set a fixed code
      console.log('Verification code: 123456');
    },
    
    showCountryCodePicker() {
      this.showCountryCodeModal = true;
    },
    
    hideCountryCodePicker() {
      this.showCountryCodeModal = false;
    },
    
    selectCountryCode(code) {
      this.countryCode = code;
      this.hideCountryCodePicker();
      this.validatePhone(); // Re-validate phone with new country code
    },
    
    validateForm() {
      let isValid = true;
      
      // Validate all fields
      this.validatePhone();
      this.validateVerificationCode();
      this.validatePassword();
      this.validateConfirmPassword();
      
      // Check agreement
      if (!this.agreeToTerms) {
        this.errors.agreement = '请阅读并同意用户协议和隐私政策';
        isValid = false;
      }
      
      // Check if any errors exist
      for (const key in this.errors) {
        if (this.errors[key]) {
          isValid = false;
          break;
        }
      }
      
      return isValid;
    },
    
    register() {
      if (!this.validateForm()) {
        // Scroll to first error
        return;
      }
      
      // Show loading
      uni.showLoading({
        title: '注册中...'
      });
      
      // Simulate registration process
      setTimeout(() => {
        uni.hideLoading();
        
        // For demo, consider any registration successful
        uni.showToast({
          title: '注册成功',
          icon: 'success',
          duration: 2000
        });
        
        // Navigate to login page after successful registration
        setTimeout(() => {
          uni.navigateBack();
        }, 2000);
      }, 1500);
    },
    
    navigateToUserAgreement() {
      uni.navigateTo({
        url: '/pages/login/user-agreement'
      });
    },
    
    navigateToPrivacyPolicy() {
      uni.navigateTo({
        url: '/pages/login/privacy-policy'
      });
    },
    
    navigateToLogin() {
      uni.navigateBack();
    }
  }
}
</script>

<style>
.register-container {
  min-height: 100vh;
  background-color: #FFFFFF;
  display: flex;
  flex-direction: column;
}

/* Header */
.register-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 30rpx;
  position: relative;
}

.back-button {
  width: 60rpx;
  height: 60rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.back-icon {
  font-size: 40rpx;
  color: #333333;
}

.page-title {
  font-size: 36rpx;
  font-weight: bold;
  color: #333333;
  position: absolute;
  left: 0;
  right: 0;
  text-align: center;
  pointer-events: none;
}

.placeholder {
  width: 60rpx;
}

/* Form */
.register-form {
  flex: 1;
  padding: 30rpx;
}

.form-group {
  margin-bottom: 40rpx;
}

.form-label {
  font-size: 28rpx;
  color: #333333;
  margin-bottom: 20rpx;
  display: block;
}

.input-container {
  display: flex;
  border-bottom: 2rpx solid #EAEAEA;
  height: 90rpx;
  align-items: center;
}

.input-container.verification {
  margin-bottom: 10rpx;
}

.country-code {
  display: flex;
  align-items: center;
  padding-right: 20rpx;
  font-size: 30rpx;
  color: #333333;
}

.down-arrow {
  margin-left: 10rpx;
  font-size: 20rpx;
  color: #999999;
}

.phone-input, .code-input, .password-input {
  flex: 1;
  height: 90rpx;
  font-size: 30rpx;
  color: #333333;
}

.get-code-button {
  background-color: #2196F3;
  color: white;
  padding: 10rpx 20rpx;
  border-radius: 10rpx;
  font-size: 26rpx;
}

.get-code-button.disabled {
  background-color: #CCCCCC;
  color: #FFFFFF;
}

.toggle-visibility {
  padding: 20rpx;
  font-size: 36rpx;
  color: #999999;
}

.error-message {
  font-size: 24rpx;
  color: #FF3B30;
  margin-top: 10rpx;
  height: 30rpx;
}

/* Agreement */
.agreement-container {
  display: flex;
  align-items: center;
  margin: 20rpx 0;
}

.checkbox {
  width: 36rpx;
  height: 36rpx;
  border: 2rpx solid #CCCCCC;
  border-radius: 6rpx;
  margin-right: 20rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.checkbox-inner {
  width: 20rpx;
  height: 20rpx;
  background-color: #2196F3;
  border-radius: 4rpx;
}

.agreement-text {
  font-size: 26rpx;
  color: #666666;
  flex: 1;
}

.link {
  color: #2196F3;
}

.agreement-error {
  margin-bottom: 20rpx;
}

/* Register Button */
.register-button {
  background-color: #2196F3;
  color: white;
  height: 90rpx;
  line-height: 90rpx;
  border-radius: 45rpx;
  font-size: 32rpx;
  margin: 60rpx 0 40rpx;
}

/* Login Link */
.login-link-container {
  text-align: center;
  font-size: 28rpx;
  color: #666666;
  margin-bottom: 40rpx;
}

.login-link {
  color: #2196F3;
  margin-left: 10rpx;
}

/* Country Code Modal */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  background-color: #FFFFFF;
  width: 80%;
  border-radius: 20rpx;
  overflow: hidden;
  max-height: 70vh;
  display: flex;
  flex-direction: column;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 30rpx;
  border-bottom: 2rpx solid #EAEAEA;
}

.modal-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333333;
}

.modal-close {
  font-size: 40rpx;
  color: #999999;
  padding: 10rpx;
}

.country-list {
  max-height: 600rpx;
}

.country-item {
  display: flex;
  justify-content: space-between;
  padding: 30rpx;
  border-bottom: 1rpx solid #EAEAEA;
}

.country-name {
  font-size: 30rpx;
  color: #333333;
}

.country-code-value {
  font-size: 30rpx;
  color: #666666;
}
</style> 