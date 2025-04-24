<template>
	<view class="notification-container">
		<!-- Header -->
		<view class="page-header">
			<view class="back-button" @click="navigateBack">
				<text class="iconfont icon-left"></text>
			</view>
			<text class="page-title">通知设置</text>
			<view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
		</view>
		
		<!-- Master Switch -->
		<view class="notification-card">
			<view class="card-header">
				<view class="header-icon bg-primary">
					<text class="iconfont icon-bell"></text>
				</view>
				<view class="header-content">
					<text class="header-title">接收通知</text>
					<text class="header-subtitle">总开关，控制所有类型通知</text>
				</view>
				<switch 
					:checked="masterSwitch" 
					color="#4a90e2" 
					@change="toggleMasterSwitch"
				/>
			</view>
			
			<view class="info-box" v-if="!masterSwitch">
				<text class="info-text">通知已关闭，您将不会收到任何通知提醒。</text>
			</view>
		</view>
		
		<!-- Notification Categories -->
		<view class="notification-card" v-if="masterSwitch">
			<view class="card-header no-border">
				<view class="header-icon bg-info">
					<text class="iconfont icon-list"></text>
				</view>
				<view class="header-content">
					<text class="header-title">通知类型</text>
					<text class="header-subtitle">选择接收的通知类别</text>
				</view>
			</view>
			
			<!-- Activity Updates -->
			<view class="notification-item">
				<view class="notification-info">
					<text class="notification-title">活动更新</text>
					<text class="notification-subtitle">新故事、游戏和学习内容上线提醒</text>
				</view>
				<switch 
					:checked="categories.activityUpdates" 
					color="#4a90e2" 
					@change="(e) => toggleCategory('activityUpdates', e)"
				/>
			</view>
			
			<!-- Learning Reminders -->
			<view class="notification-item">
				<view class="notification-info">
					<text class="notification-title">学习提醒</text>
					<text class="notification-subtitle">日常学习计划和目标完成提醒</text>
				</view>
				<switch 
					:checked="categories.learningReminders" 
					color="#4a90e2" 
					@change="(e) => toggleCategory('learningReminders', e)"
				/>
			</view>
			
			<!-- Usage Reports -->
			<view class="notification-item">
				<view class="notification-info">
					<text class="notification-title">使用报告</text>
					<text class="notification-subtitle">每日/每周使用时长统计报告</text>
				</view>
				<switch 
					:checked="categories.usageReports" 
					color="#4a90e2" 
					@change="(e) => toggleCategory('usageReports', e)"
				/>
			</view>
			
			<!-- System Notifications -->
			<view class="notification-item">
				<view class="notification-info">
					<text class="notification-title">系统通知</text>
					<text class="notification-subtitle">账户、安全和系统更新提醒</text>
				</view>
				<switch 
					:checked="categories.systemNotifications" 
					color="#4a90e2" 
					@change="(e) => toggleCategory('systemNotifications', e)"
				/>
			</view>
			
			<!-- Promotional Messages -->
			<view class="notification-item">
				<view class="notification-info">
					<text class="notification-title">促销消息</text>
					<text class="notification-subtitle">优惠活动和特别推广提醒</text>
				</view>
				<switch 
					:checked="categories.promotionalMessages" 
					color="#4a90e2" 
					@change="(e) => toggleCategory('promotionalMessages', e)"
				/>
			</view>
		</view>
		
		<!-- Quiet Hours -->
		<view class="notification-card" v-if="masterSwitch">
			<view class="card-header">
				<view class="header-icon bg-warning">
					<text class="iconfont icon-moon"></text>
				</view>
				<view class="header-content">
					<text class="header-title">勿扰时段</text>
					<text class="header-subtitle">在指定时段内不发送通知</text>
				</view>
				<switch 
					:checked="quietHours.enabled" 
					color="#4a90e2" 
					@change="toggleQuietHours"
				/>
			</view>
			
			<view class="quiet-hours-settings" v-if="quietHours.enabled">
				<view class="time-range">
					<view class="time-picker">
						<text class="time-label">从</text>
						<picker 
							mode="time" 
							:value="quietHours.startTime" 
							@change="handleStartTimeChange"
						>
							<view class="picker-value">
								<text>{{ quietHours.startTime }}</text>
								<text class="iconfont icon-chevron-down"></text>
							</view>
						</picker>
					</view>
					<view class="time-picker">
						<text class="time-label">至</text>
						<picker 
							mode="time" 
							:value="quietHours.endTime" 
							@change="handleEndTimeChange"
						>
							<view class="picker-value">
								<text>{{ quietHours.endTime }}</text>
								<text class="iconfont icon-chevron-down"></text>
							</view>
						</picker>
					</view>
				</view>
				
				<view class="days-selector">
					<text class="days-label">应用到以下日期</text>
					<view class="days-grid">
						<view 
							v-for="(day, index) in daysOfWeek" 
							:key="index"
							:class="['day-item', quietHours.allowedDays.includes(index) ? 'selected' : '']"
							@click="toggleAllowedDay(index)"
						>
							{{ day }}
						</view>
					</view>
				</view>
				
				<view class="exception-settings">
					<text class="exception-label">例外情况</text>
					<view class="exception-item">
						<text>允许重要通知</text>
						<switch 
							:checked="quietHours.allowImportant" 
							color="#4a90e2" 
							@change="toggleAllowImportant"
						/>
					</view>
					<view class="exception-item">
						<text>允许重复提醒</text>
						<switch 
							:checked="quietHours.allowRepeated" 
							color="#4a90e2" 
							@change="toggleAllowRepeated"
						/>
					</view>
				</view>
			</view>
		</view>
		
		<!-- Notification Style -->
		<view class="notification-card" v-if="masterSwitch">
			<view class="card-header no-border">
				<view class="header-icon bg-success">
					<text class="iconfont icon-cog"></text>
				</view>
				<view class="header-content">
					<text class="header-title">通知样式</text>
					<text class="header-subtitle">自定义通知的显示方式</text>
				</view>
			</view>
			
			<view class="style-settings">
				<view class="style-option">
					<text class="option-label">显示横幅</text>
					<switch 
						:checked="style.showBanner" 
						color="#4a90e2" 
						@change="toggleShowBanner"
					/>
				</view>
				
				<view class="style-option">
					<text class="option-label">显示预览内容</text>
					<switch 
						:checked="style.showPreview" 
						color="#4a90e2" 
						@change="toggleShowPreview"
					/>
				</view>
				
				<view class="style-option">
					<text class="option-label">声音提醒</text>
					<switch 
						:checked="style.playSound" 
						color="#4a90e2" 
						@change="togglePlaySound"
					/>
				</view>
				
				<view class="style-option">
					<text class="option-label">振动提醒</text>
					<switch 
						:checked="style.vibrate" 
						color="#4a90e2" 
						@change="toggleVibrate"
					/>
				</view>
				
				<view class="sound-selector" v-if="style.playSound">
					<text class="sound-label">通知提示音</text>
					<picker 
						mode="selector" 
						:range="soundOptions" 
						:value="style.soundIndex" 
						@change="handleSoundChange"
					>
						<view class="picker-value wide-picker">
							<text>{{ soundOptions[style.soundIndex] }}</text>
							<text class="iconfont icon-chevron-down"></text>
						</view>
					</picker>
				</view>
			</view>
		</view>
		
		<!-- Email Notifications -->
		<view class="notification-card" v-if="masterSwitch">
			<view class="card-header">
				<view class="header-icon bg-secondary">
					<text class="iconfont icon-mail"></text>
				</view>
				<view class="header-content">
					<text class="header-title">邮件通知</text>
					<text class="header-subtitle">通过邮件接收重要提醒</text>
				</view>
				<switch 
					:checked="emailNotifications.enabled" 
					color="#4a90e2" 
					@change="toggleEmailNotifications"
				/>
			</view>
			
			<view class="email-settings" v-if="emailNotifications.enabled">
				<view class="email-input">
					<text class="email-label">接收邮箱</text>
					<input 
						type="text" 
						v-model="emailNotifications.email" 
						placeholder="请输入邮箱地址" 
						class="input"
					/>
				</view>
				
				<view class="frequency-selector">
					<text class="frequency-label">发送频率</text>
					<radio-group @change="handleFrequencyChange">
						<view class="frequency-option">
							<radio :checked="emailNotifications.frequency === 'immediate'" value="immediate" color="#4a90e2" />
							<text>实时发送</text>
						</view>
						<view class="frequency-option">
							<radio :checked="emailNotifications.frequency === 'daily'" value="daily" color="#4a90e2" />
							<text>每日摘要</text>
						</view>
						<view class="frequency-option">
							<radio :checked="emailNotifications.frequency === 'weekly'" value="weekly" color="#4a90e2" />
							<text>每周摘要</text>
						</view>
					</radio-group>
				</view>
			</view>
		</view>
		
		<!-- Save Button -->
		<button class="save-button" @click="saveSettings">保存设置</button>
	</view>
</template>

<script>
export default {
	data() {
		return {
			masterSwitch: true,
			daysOfWeek: ['日', '一', '二', '三', '四', '五', '六'],
			soundOptions: ['默认提示音', '清脆铃声', '温柔提示', '轻快旋律', '无声'],
			categories: {
				activityUpdates: true,
				learningReminders: true,
				usageReports: true,
				systemNotifications: true,
				promotionalMessages: false
			},
			quietHours: {
				enabled: false,
				startTime: '22:00',
				endTime: '07:00',
				allowedDays: [0, 1, 2, 3, 4, 5, 6], // All days selected by default
				allowImportant: true,
				allowRepeated: false
			},
			style: {
				showBanner: true,
				showPreview: true,
				playSound: true,
				vibrate: true,
				soundIndex: 0
			},
			emailNotifications: {
				enabled: false,
				email: '',
				frequency: 'daily'
			}
		}
	},
	methods: {
		navigateBack() {
			uni.navigateBack()
		},
		toggleMasterSwitch(e) {
			this.masterSwitch = e.detail.value
		},
		toggleCategory(category, e) {
			this.categories[category] = e.detail.value
		},
		toggleQuietHours(e) {
			this.quietHours.enabled = e.detail.value
		},
		handleStartTimeChange(e) {
			this.quietHours.startTime = e.detail.value
		},
		handleEndTimeChange(e) {
			this.quietHours.endTime = e.detail.value
		},
		toggleAllowedDay(index) {
			const position = this.quietHours.allowedDays.indexOf(index)
			if (position === -1) {
				// Add day
				this.quietHours.allowedDays.push(index)
			} else {
				// Remove day
				this.quietHours.allowedDays.splice(position, 1)
			}
		},
		toggleAllowImportant(e) {
			this.quietHours.allowImportant = e.detail.value
		},
		toggleAllowRepeated(e) {
			this.quietHours.allowRepeated = e.detail.value
		},
		toggleShowBanner(e) {
			this.style.showBanner = e.detail.value
		},
		toggleShowPreview(e) {
			this.style.showPreview = e.detail.value
		},
		togglePlaySound(e) {
			this.style.playSound = e.detail.value
		},
		toggleVibrate(e) {
			this.style.vibrate = e.detail.value
		},
		handleSoundChange(e) {
			this.style.soundIndex = parseInt(e.detail.value)
		},
		toggleEmailNotifications(e) {
			this.emailNotifications.enabled = e.detail.value
		},
		handleFrequencyChange(e) {
			this.emailNotifications.frequency = e.detail.value
		},
		saveSettings() {
			// Validate settings
			if (this.emailNotifications.enabled && !this.emailNotifications.email) {
				uni.showToast({
					title: '请输入邮箱地址',
					icon: 'none'
				})
				return
			}
			
			if (this.quietHours.enabled && this.quietHours.allowedDays.length === 0) {
				uni.showToast({
					title: '请至少选择一天',
					icon: 'none'
				})
				return
			}
			
			uni.showLoading({
				title: '保存中...'
			})
			
			// Simulate saving to server
			setTimeout(() => {
				uni.hideLoading()
				uni.showToast({
					title: '设置已保存',
					icon: 'success'
				})
				
				// Navigate back after saving
				setTimeout(() => {
					uni.navigateBack()
				}, 1500)
			}, 1500)
		}
	}
}
</script>

<style>
.notification-container {
	background-color: #f5f5f5;
	min-height: 100vh;
	padding-bottom: 40rpx;
}

.page-header {
	display: flex;
	align-items: center;
	justify-content: space-between;
	padding: 30rpx;
	background-color: #fff;
}

.back-button {
	font-size: 36rpx;
	color: #212529;
	padding: 10rpx;
}

.page-title {
	font-size: 36rpx;
	font-weight: 600;
}

.notification-card {
	background-color: #fff;
	margin: 30rpx;
	border-radius: 20rpx;
	overflow: hidden;
	box-shadow: 0 4rpx 10rpx rgba(0,0,0,0.05);
}

.card-header {
	display: flex;
	align-items: center;
	padding: 30rpx;
	border-bottom: 1rpx solid #f5f5f5;
}

.card-header.no-border {
	border-bottom: none;
}

.header-icon {
	width: 80rpx;
	height: 80rpx;
	border-radius: 20rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	margin-right: 20rpx;
	color: white;
	font-size: 36rpx;
}

.bg-primary {
	background-color: #4a90e2;
}

.bg-secondary {
	background-color: #6c757d;
}

.bg-success {
	background-color: #28a745;
}

.bg-danger {
	background-color: #dc3545;
}

.bg-warning {
	background-color: #ffc107;
}

.bg-info {
	background-color: #17a2b8;
}

.header-content {
	flex: 1;
}

.header-title {
	font-size: 32rpx;
	font-weight: 600;
	margin-bottom: 6rpx;
	display: block;
}

.header-subtitle {
	font-size: 26rpx;
	color: #6c757d;
}

.info-box {
	padding: 20rpx 30rpx 30rpx;
	background-color: #f8f9fa;
}

.info-text {
	font-size: 28rpx;
	color: #6c757d;
}

.notification-item {
	display: flex;
	align-items: center;
	padding: 30rpx;
	border-bottom: 1rpx solid #f5f5f5;
}

.notification-item:last-child {
	border-bottom: none;
}

.notification-info {
	flex: 1;
}

.notification-title {
	font-size: 30rpx;
	font-weight: 500;
	margin-bottom: 6rpx;
	display: block;
}

.notification-subtitle {
	font-size: 26rpx;
	color: #6c757d;
}

.quiet-hours-settings, .email-settings {
	padding: 30rpx;
}

.time-range {
	display: flex;
	justify-content: space-between;
	margin-bottom: 30rpx;
}

.time-picker {
	display: flex;
	align-items: center;
	width: 48%;
}

.time-label {
	font-size: 30rpx;
	margin-right: 20rpx;
}

.picker-value {
	flex: 1;
	height: 80rpx;
	border: 1rpx solid #dee2e6;
	border-radius: 10rpx;
	padding: 0 20rpx;
	font-size: 30rpx;
	background-color: #f8f9fa;
	display: flex;
	align-items: center;
	justify-content: space-between;
}

.wide-picker {
	width: 100%;
}

.days-selector {
	margin-bottom: 30rpx;
}

.days-label, .exception-label, .sound-label, .email-label, .frequency-label {
	font-size: 30rpx;
	font-weight: 500;
	margin-bottom: 20rpx;
	display: block;
}

.days-grid {
	display: flex;
	flex-wrap: wrap;
	gap: 20rpx;
}

.day-item {
	width: 80rpx;
	height: 80rpx;
	border-radius: 40rpx;
	border: 1rpx solid #dee2e6;
	display: flex;
	align-items: center;
	justify-content: center;
	font-size: 30rpx;
	color: #6c757d;
}

.day-item.selected {
	background-color: #4a90e2;
	color: white;
	border-color: #4a90e2;
}

.exception-item {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 20rpx 0;
	border-bottom: 1rpx solid #f5f5f5;
	font-size: 30rpx;
}

.exception-item:last-child {
	border-bottom: none;
}

.style-settings {
	padding: 30rpx;
}

.style-option {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 20rpx 0;
	border-bottom: 1rpx solid #f5f5f5;
	font-size: 30rpx;
}

.style-option:last-child {
	border-bottom: none;
}

.option-label {
	font-size: 30rpx;
}

.sound-selector {
	margin-top: 20rpx;
}

.email-input {
	margin-bottom: 30rpx;
}

.input {
	height: 80rpx;
	border: 1rpx solid #dee2e6;
	border-radius: 10rpx;
	padding: 0 20rpx;
	font-size: 30rpx;
	background-color: #f8f9fa;
	width: 100%;
	box-sizing: border-box;
}

.frequency-option {
	display: flex;
	align-items: center;
	margin-bottom: 20rpx;
	font-size: 30rpx;
}

.frequency-option radio {
	transform: scale(0.8);
	margin-right: 10rpx;
}

.save-button {
	margin: 30rpx;
	height: 90rpx;
	background-color: #4a90e2;
	color: white;
	border-radius: 45rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	font-size: 32rpx;
	font-weight: 500;
}
</style> 