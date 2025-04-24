<template>
	<view class="parental-control-container">
		<!-- Header -->
		<view class="header">
			<view class="back-button" @click="navigateBack">
				<image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
			</view>
			<text class="header-title">家长控制</text>
		</view>
		
		<!-- PIN Protection -->
		<view class="section-card">
			<view class="section-header">
				<text class="section-title">PIN码保护</text>
				<switch 
					:checked="settings.pinEnabled" 
					@change="togglePinEnabled" 
					color="#2196f3"
				/>
			</view>
			<view class="section-content" v-if="settings.pinEnabled">
				<view class="form-item">
					<text class="form-label">设置PIN码</text>
					<view class="pin-input-container">
						<input 
							v-for="(digit, index) in pinDigits" 
							:key="index"
							class="pin-digit-input"
							type="number"
							maxlength="1"
							:value="digit"
							:focus="currentPinFocus === index"
							@input="updatePinDigit(index, $event)"
							@focus="currentPinFocus = index"
						/>
					</view>
					<text class="form-description">访问家长控制和其他设置时需要输入PIN码</text>
				</view>
			</view>
		</view>
		
		<!-- Time Limits -->
		<view class="section-card">
			<view class="section-header">
				<text class="section-title">使用时间限制</text>
				<switch 
					:checked="settings.timeLimitEnabled" 
					@change="toggleTimeLimitEnabled" 
					color="#2196f3"
				/>
			</view>
			<view class="section-content" v-if="settings.timeLimitEnabled">
				<view class="form-item">
					<text class="form-label">每日使用时间</text>
					<picker 
						mode="selector" 
						:range="timeOptions" 
						@change="onDailyLimitChange"
						:value="dailyLimitIndex"
						class="time-picker"
					>
						<view class="picker-view">
							<text>{{ settings.dailyLimit }}分钟</text>
							<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
						</view>
					</picker>
					<text class="form-description">设置孩子每天可以使用应用的总时长</text>
				</view>
				
				<view class="form-item">
					<text class="form-label">休息提醒</text>
					<picker 
						mode="selector" 
						:range="breakOptions" 
						@change="onBreakReminderChange"
						:value="breakReminderIndex"
						class="time-picker"
					>
						<view class="picker-view">
							<text>每{{ settings.breakReminder }}分钟</text>
							<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
						</view>
					</picker>
					<text class="form-description">设置连续使用一段时间后提醒休息</text>
				</view>
				
				<view class="form-item">
					<text class="form-label">就寝时间</text>
					<view class="bedtime-container">
						<picker 
							mode="time" 
							@change="onBedtimeChange"
							:value="settings.bedtime"
							class="time-picker"
						>
							<view class="picker-view">
								<text>{{ settings.bedtime }}</text>
								<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
							</view>
						</picker>
						<text class="time-separator">至</text>
						<picker 
							mode="time" 
							@change="onWakeTimeChange"
							:value="settings.wakeTime"
							class="time-picker"
						>
							<view class="picker-view">
								<text>{{ settings.wakeTime }}</text>
								<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
							</view>
						</picker>
					</view>
					<text class="form-description">设置设备不可用的就寝时间段</text>
				</view>
			</view>
		</view>
		
		<!-- Content Filtering -->
		<view class="section-card">
			<view class="section-header">
				<text class="section-title">内容过滤</text>
				<switch 
					:checked="settings.contentFilterEnabled" 
					@change="toggleContentFilterEnabled" 
					color="#2196f3"
				/>
			</view>
			<view class="section-content" v-if="settings.contentFilterEnabled">
				<view class="form-item">
					<text class="form-label">年龄分级</text>
					<picker 
						mode="selector" 
						:range="ageRatingOptions" 
						@change="onAgeRatingChange"
						:value="ageRatingIndex"
						class="age-picker"
					>
						<view class="picker-view">
							<text>{{ settings.ageRating }}岁及以下</text>
							<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
						</view>
					</picker>
					<text class="form-description">仅显示适合该年龄段的内容</text>
				</view>
				
				<view class="form-item no-border">
					<text class="form-label">受限内容类别</text>
					<view class="category-list">
						<view 
							v-for="(category, index) in contentCategories" 
							:key="index"
							class="category-item"
							:class="{ 'category-selected': isCategoryBlocked(category.value) }"
							@click="toggleCategory(category.value)"
						>
							<text>{{ category.label }}</text>
						</view>
					</view>
					<text class="form-description">选择需要限制的内容类别</text>
				</view>
			</view>
		</view>
		
		<!-- App Access -->
		<view class="section-card">
			<view class="section-header">
				<text class="section-title">应用功能控制</text>
			</view>
			<view class="section-content">
				<view class="feature-list">
					<view class="feature-item" v-for="(feature, index) in appFeatures" :key="index">
						<view class="feature-info">
							<text class="feature-name">{{ feature.name }}</text>
							<text class="feature-description">{{ feature.description }}</text>
						</view>
						<switch 
							:checked="isFeatureEnabled(feature.id)" 
							@change="(e) => toggleFeature(feature.id, e)" 
							color="#2196f3"
						/>
					</view>
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
			settings: {
				pinEnabled: false,
				pin: '0000',
				timeLimitEnabled: false,
				dailyLimit: 60,
				breakReminder: 30,
				bedtime: '21:00',
				wakeTime: '07:00',
				contentFilterEnabled: false,
				ageRating: 5,
				blockedCategories: [],
				enabledFeatures: ['chat', 'stories', 'learning', 'games']
			},
			pinDigits: ['', '', '', ''],
			currentPinFocus: 0,
			timeOptions: ['30', '45', '60', '90', '120', '150', '180'],
			breakOptions: ['15', '30', '45', '60'],
			ageRatingOptions: [3, 4, 5, 6, 7, 8, 9, 10, 11, 12],
			contentCategories: [
				{ label: '暴力内容', value: 'violence' },
				{ label: '恐怖内容', value: 'horror' },
				{ label: '购买行为', value: 'purchase' },
				{ label: '社交网络', value: 'social' },
				{ label: '外部链接', value: 'externalLinks' }
			],
			appFeatures: [
				{ id: 'chat', name: '智能对话', description: '与AI助手进行对话' },
				{ id: 'stories', name: '故事阅读', description: '阅读和听故事' },
				{ id: 'learning', name: '学习内容', description: '访问学习课程和活动' },
				{ id: 'games', name: '互动游戏', description: '玩教育类游戏' }
			]
		}
	},
	computed: {
		dailyLimitIndex() {
			return this.timeOptions.indexOf(this.settings.dailyLimit.toString());
		},
		breakReminderIndex() {
			return this.breakOptions.indexOf(this.settings.breakReminder.toString());
		},
		ageRatingIndex() {
			return this.ageRatingOptions.indexOf(this.settings.ageRating);
		}
	},
	onLoad() {
		// Load settings from storage if available
		const storedSettings = uni.getStorageSync('parentalSettings');
		if (storedSettings) {
			this.settings = {
				...this.settings,
				...storedSettings
			};
			
			// Convert PIN to digits array
			if (this.settings.pin) {
				this.pinDigits = this.settings.pin.split('').map(d => d);
			}
		}
	},
	methods: {
		navigateBack() {
			uni.navigateBack();
		},
		togglePinEnabled(e) {
			this.settings.pinEnabled = e.detail.value;
			if (this.settings.pinEnabled) {
				// Focus on first PIN input when enabled
				this.currentPinFocus = 0;
				// Reset PIN if disabled previously
				if (!this.settings.pin || this.settings.pin === '0000') {
					this.pinDigits = ['', '', '', ''];
				}
			}
		},
		updatePinDigit(index, e) {
			const value = e.detail.value;
			if (value) {
				this.pinDigits[index] = value;
				
				// Move focus to next input
				if (index < 3) {
					this.currentPinFocus = index + 1;
				} else {
					// All digits entered
					this.settings.pin = this.pinDigits.join('');
				}
			}
		},
		toggleTimeLimitEnabled(e) {
			this.settings.timeLimitEnabled = e.detail.value;
		},
		onDailyLimitChange(e) {
			const index = e.detail.value;
			this.settings.dailyLimit = parseInt(this.timeOptions[index]);
		},
		onBreakReminderChange(e) {
			const index = e.detail.value;
			this.settings.breakReminder = parseInt(this.breakOptions[index]);
		},
		onBedtimeChange(e) {
			this.settings.bedtime = e.detail.value;
		},
		onWakeTimeChange(e) {
			this.settings.wakeTime = e.detail.value;
		},
		toggleContentFilterEnabled(e) {
			this.settings.contentFilterEnabled = e.detail.value;
		},
		onAgeRatingChange(e) {
			const index = e.detail.value;
			this.settings.ageRating = this.ageRatingOptions[index];
		},
		isCategoryBlocked(category) {
			return this.settings.blockedCategories.includes(category);
		},
		toggleCategory(category) {
			if (this.isCategoryBlocked(category)) {
				this.settings.blockedCategories = this.settings.blockedCategories.filter(c => c !== category);
			} else {
				this.settings.blockedCategories.push(category);
			}
		},
		isFeatureEnabled(featureId) {
			return this.settings.enabledFeatures.includes(featureId);
		},
		toggleFeature(featureId, e) {
			const enabled = e.detail.value;
			
			if (enabled) {
				if (!this.isFeatureEnabled(featureId)) {
					this.settings.enabledFeatures.push(featureId);
				}
			} else {
				this.settings.enabledFeatures = this.settings.enabledFeatures.filter(id => id !== featureId);
			}
		},
		saveSettings() {
			// Validate PIN if enabled
			if (this.settings.pinEnabled) {
				const pin = this.pinDigits.join('');
				if (pin.length !== 4) {
					uni.showToast({
						title: '请输入完整的4位PIN码',
						icon: 'none'
					});
					return;
				}
				this.settings.pin = pin;
			}
			
			// Save to storage
			uni.setStorageSync('parentalSettings', this.settings);
			
			uni.showToast({
				title: '设置已保存',
				icon: 'success'
			});
			
			// Navigate back after short delay
			setTimeout(() => {
				uni.navigateBack();
			}, 1500);
		}
	}
}
</script>

<style>
.parental-control-container {
	min-height: 100vh;
	background-color: #f5f5f5;
	padding-bottom: 40rpx;
}

.header {
	position: relative;
	display: flex;
	align-items: center;
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
	flex: 1;
	text-align: center;
	font-size: 36rpx;
	font-weight: bold;
	color: #333;
	margin-right: 50rpx; /* Offset for back button to center title */
}

.section-card {
	background-color: #fff;
	margin-top: 20rpx;
	padding: 0 40rpx;
}

.section-header {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 30rpx 0;
	border-bottom: 1px solid #eee;
}

.section-title {
	font-size: 32rpx;
	font-weight: bold;
	color: #333;
}

.section-content {
	padding: 20rpx 0;
}

.form-item {
	padding: 20rpx 0;
	border-bottom: 1px solid #eee;
}

.form-item.no-border {
	border-bottom: none;
}

.form-label {
	font-size: 30rpx;
	color: #333;
	margin-bottom: 20rpx;
	display: block;
}

.form-description {
	font-size: 26rpx;
	color: #999;
	margin-top: 10rpx;
	display: block;
}

.pin-input-container {
	display: flex;
	justify-content: space-between;
	margin: 30rpx 0;
}

.pin-digit-input {
	width: 100rpx;
	height: 100rpx;
	background-color: #f5f5f5;
	border-radius: 10rpx;
	text-align: center;
	font-size: 40rpx;
	font-weight: bold;
}

.picker-view {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 20rpx 0;
	border-bottom: 1px solid #eee;
	font-size: 32rpx;
	color: #333;
}

.arrow-icon {
	width: 32rpx;
	height: 32rpx;
}

.bedtime-container {
	display: flex;
	align-items: center;
	justify-content: space-between;
}

.time-separator {
	padding: 0 20rpx;
	color: #666;
	font-size: 28rpx;
}

.bedtime-container .picker-view {
	flex: 1;
}

.category-list {
	display: flex;
	flex-wrap: wrap;
	gap: 20rpx;
	margin: 20rpx 0;
}

.category-item {
	padding: 15rpx 30rpx;
	border-radius: 30rpx;
	background-color: #f0f0f0;
	font-size: 28rpx;
	color: #666;
}

.category-selected {
	background-color: #e3f2fd;
	color: #2196f3;
}

.feature-list {
	display: flex;
	flex-direction: column;
}

.feature-item {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 30rpx 0;
	border-bottom: 1px solid #eee;
}

.feature-item:last-child {
	border-bottom: none;
}

.feature-info {
	flex: 1;
}

.feature-name {
	font-size: 30rpx;
	color: #333;
	margin-bottom: 10rpx;
	display: block;
}

.feature-description {
	font-size: 26rpx;
	color: #999;
}

.save-button {
	background-color: #2196f3;
	color: white;
	height: 90rpx;
	line-height: 90rpx;
	font-size: 32rpx;
	border-radius: 45rpx;
	margin: 60rpx 40rpx;
}
</style> 