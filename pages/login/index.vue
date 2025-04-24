<template>
	<view class="login-container">
		<!-- Logo and App Name -->
		<view class="login-header">
			<image src="/static/images/app-logo.png" mode="aspectFit" class="app-logo"></image>
			<text class="app-name">小智App</text>
			<text class="app-slogan">儿童AI伴学应用</text>
		</view>
		
		<!-- Login Form -->
		<view class="login-form">
			<!-- Username/Email Input -->
			<view class="form-item">
				<view class="input-icon">👤</view>
				<input 
					type="text" 
					v-model="username" 
					placeholder="请输入手机号/邮箱"
					class="form-input"
					@input="clearError"
				/>
			</view>
			
			<!-- Password Input -->
			<view class="form-item">
				<view class="input-icon">🔒</view>
				<input 
					:type="showPassword ? 'text' : 'password'" 
					v-model="password" 
					placeholder="请输入密码"
					class="form-input"
					@input="clearError"
				/>
				<view class="password-toggle" @tap="togglePasswordVisibility">
					<text>{{ showPassword ? '👁️' : '👁️‍��️' }}</text>
				</view>
			</view>
			
			<!-- Error Message -->
			<view class="error-message" v-if="errorMessage">
				<text>{{ errorMessage }}</text>
			</view>
			
			<!-- Remember me and Forgot Password -->
			<view class="form-options">
				<view class="remember-me">
					<view class="checkbox" @tap="toggleRememberMe">
						<view class="checkbox-inner" v-if="rememberMe"></view>
					</view>
					<text>记住我</text>
				</view>
				<text class="forgot-password" @tap="navigateToForgotPassword">忘记密码？</text>
			</view>
			
			<!-- Login Button -->
			<button class="login-button" @tap="handleLogin">登录</button>
		</view>
		
		<!-- Or Divider -->
		<view class="divider">
			<view class="divider-line"></view>
			<text class="divider-text">其他登录方式</text>
			<view class="divider-line"></view>
		</view>
		
		<!-- Social Login Options -->
		<view class="social-login">
			<view class="social-icons">
				<view class="social-icon wechat" @tap="handleSocialLogin('wechat')">
					<image src="/static/icons/wechat.png" mode="aspectFit"></image>
				</view>
				<view class="social-icon qq" @tap="handleSocialLogin('qq')">
					<image src="/static/icons/qq.png" mode="aspectFit"></image>
				</view>
				<view class="social-icon weibo" @tap="handleSocialLogin('weibo')">
					<image src="/static/icons/weibo.png" mode="aspectFit"></image>
				</view>
			</view>
		</view>
		
		<!-- Registration Link -->
		<view class="register-section">
			<text>还没有账号？</text>
			<text class="register-link" @tap="navigateToRegister">立即注册</text>
		</view>
		
		<!-- Terms and Privacy -->
		<view class="terms-privacy">
			<text>登录即表示您同意</text>
			<text class="terms-link" @tap="navigateToUserAgreement">用户协议</text>
			<text>和</text>
			<text class="terms-link" @tap="navigateToPrivacyPolicy">隐私政策</text>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			username: '',
			password: '',
			showPassword: false,
			rememberMe: false,
			errorMessage: '',
			isLoading: false
		}
	},
	onLoad() {
		// Check if user was remembered
		const rememberedUser = uni.getStorageSync('rememberedUser');
		if (rememberedUser) {
			this.username = rememberedUser;
			this.rememberMe = true;
		}
		
		// Check if already logged in
		const isLoggedIn = uni.getStorageSync('isLoggedIn');
		if (isLoggedIn) {
			uni.switchTab({
				url: '/pages/index/index'
			});
		}
	},
	methods: {
		togglePasswordVisibility() {
			this.showPassword = !this.showPassword;
		},
		toggleRememberMe() {
			this.rememberMe = !this.rememberMe;
		},
		clearError() {
			this.errorMessage = '';
		},
		validateForm() {
			if (!this.username.trim()) {
				this.errorMessage = '请输入用户名';
				return false;
			}
			
			if (!this.password.trim()) {
				this.errorMessage = '请输入密码';
				return false;
			}
			
			return true;
		},
		handleLogin() {
			if (!this.validateForm()) return;
			
			this.isLoading = true;
			
			// Simulate API call
			setTimeout(() => {
				this.isLoading = false;
				
				// Demo login success with test account
				if (this.username === 'demo' && this.password === 'password') {
					// Store login state
					uni.setStorageSync('isLoggedIn', true);
					if (this.rememberMe) {
						uni.setStorageSync('rememberedUser', this.username);
					} else {
						uni.removeStorageSync('rememberedUser');
					}
					
					// Navigate to home page
					uni.switchTab({
						url: '/pages/index/index'
					});
				} else {
					this.errorMessage = '用户名或密码错误';
				}
			}, 1500);
		},
		handleSocialLogin(platform) {
			// In a real app, this would integrate with the respective platform's SDK
			console.log(`Logging in with ${platform}`);
			
			// For demo, simulate successful login
			uni.showToast({
				title: `正在使用${platform}登录`,
				icon: 'none',
				duration: 1500
			});
			
			setTimeout(() => {
				uni.setStorageSync('isLoggedIn', true);
				uni.switchTab({
					url: '/pages/index/index'
				});
			}, 1500);
		},
		navigateToForgotPassword() {
			uni.navigateTo({
				url: '/pages/login/forgot-password'
			});
		},
		navigateToRegister() {
			uni.navigateTo({
				url: '/pages/login/register'
			});
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
		}
	}
}
</script>

<style>
.login-container {
	min-height: 100vh;
	padding: 60rpx 50rpx;
	display: flex;
	flex-direction: column;
	background-color: #ffffff;
}

.login-header {
	display: flex;
	flex-direction: column;
	align-items: center;
	margin-bottom: 80rpx;
	margin-top: 40rpx;
}

.app-logo {
	width: 160rpx;
	height: 160rpx;
	margin-bottom: 30rpx;
}

.app-name {
	font-size: 48rpx;
	font-weight: bold;
	color: #333333;
	margin-bottom: 10rpx;
}

.app-slogan {
	font-size: 28rpx;
	color: #999999;
}

.login-form {
	margin-bottom: 60rpx;
}

.form-item {
	display: flex;
	align-items: center;
	border-bottom: 1rpx solid #EAEAEA;
	padding: 20rpx 0;
	margin-bottom: 30rpx;
	position: relative;
}

.input-icon {
	margin-right: 20rpx;
	font-size: 36rpx;
	color: #999999;
}

.form-input {
	flex: 1;
	height: 60rpx;
	font-size: 30rpx;
}

.password-toggle {
	font-size: 36rpx;
	color: #999999;
	padding: 10rpx;
}

.error-message {
	color: #FF3B30;
	font-size: 26rpx;
	margin-bottom: 20rpx;
}

.form-options {
	display: flex;
	justify-content: space-between;
	align-items: center;
	margin-bottom: 40rpx;
}

.remember-me {
	display: flex;
	align-items: center;
}

.checkbox {
	width: 36rpx;
	height: 36rpx;
	border: 2rpx solid #CCCCCC;
	border-radius: 6rpx;
	margin-right: 10rpx;
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

.forgot-password {
	color: #2196F3;
	font-size: 26rpx;
}

.login-button {
	background-color: #2196F3;
	color: white;
	height: 90rpx;
	border-radius: 45rpx;
	font-size: 32rpx;
	display: flex;
	align-items: center;
	justify-content: center;
}

.divider {
	display: flex;
	align-items: center;
	margin-bottom: 40rpx;
}

.divider-line {
	flex: 1;
	height: 1rpx;
	background-color: #EAEAEA;
}

.divider-text {
	padding: 0 20rpx;
	color: #999999;
	font-size: 26rpx;
}

.social-login {
	margin-bottom: 60rpx;
}

.social-icons {
	display: flex;
	justify-content: center;
}

.social-icon {
	width: 100rpx;
	height: 100rpx;
	border-radius: 50rpx;
	margin: 0 30rpx;
	display: flex;
	align-items: center;
	justify-content: center;
}

.social-icon image {
	width: 60rpx;
	height: 60rpx;
}

.social-icon.wechat {
	background-color: #07C160;
}

.social-icon.qq {
	background-color: #12B7F5;
}

.social-icon.weibo {
	background-color: #E6162D;
}

.register-section {
	display: flex;
	justify-content: center;
	font-size: 28rpx;
	color: #333333;
	margin-bottom: 40rpx;
}

.register-link {
	color: #2196F3;
	margin-left: 10rpx;
}

.terms-privacy {
	text-align: center;
	font-size: 24rpx;
	color: #999999;
	margin-top: auto;
	padding: 20rpx 0;
}

.terms-link {
	color: #2196F3;
	margin: 0 5rpx;
}
</style> 