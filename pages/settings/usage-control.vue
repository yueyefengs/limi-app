<template>
	<view class="usage-control-container">
		<!-- Header -->
		<view class="header">
			<view class="back-button" @click="navigateBack">
				<image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
			</view>
			<text class="header-title">使用控制</text>
			<view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
		</view>
		
		<!-- Main Content -->
		<scroll-view scroll-y class="control-content">
			<!-- Enable Usage Control -->
			<view class="control-card">
				<view class="control-header">
					<text class="control-title">使用时间控制</text>
					<switch :checked="isControlEnabled" @change="toggleControl" color="#2196f3" />
				</view>
				<view class="control-description">
					<text>开启后，将限制应用的使用时间，帮助孩子培养健康的使用习惯</text>
				</view>
			</view>
			
			<!-- Usage Statistics -->
			<view class="stats-card" v-if="isControlEnabled">
				<view class="stats-header">
					<text class="stats-title">本周使用情况</text>
					<text class="view-details" @click="showWeeklyReport">查看详情</text>
				</view>
				
				<view class="weekly-chart">
					<view class="chart-bars">
						<view class="day-column" v-for="(day, index) in weeklyUsage" :key="index">
							<view class="usage-bar-container">
								<view class="usage-bar" :style="{ height: (day.minutes / maxDailyLimit * 100) + '%' }">
									<view 
										class="bar-segment"
										:class="{ 'over-limit': day.minutes > dailyTimeLimit }"
										:style="{ 
											height: (Math.min(day.minutes, maxDailyLimit) / maxDailyLimit * 100) + '%',
											'background-color': day.minutes > dailyTimeLimit ? '#ff6b6b' : '#2196f3'
										}"
									></view>
								</view>
							</view>
							<text class="day-label">{{ day.label }}</text>
							<text class="usage-time">{{ day.minutes }}分钟</text>
						</view>
					</view>
					
					<view class="limit-line" :style="{ bottom: (dailyTimeLimit / maxDailyLimit * 100) + '%' }">
						<text class="limit-label">每日限制</text>
					</view>
				</view>
			</view>
			
			<!-- Daily Time Limit -->
			<view class="setting-card" v-if="isControlEnabled">
				<view class="setting-header">
					<text class="setting-title">每日使用时长</text>
					<text class="time-display">{{ dailyTimeLimit }}分钟</text>
				</view>
				
				<view class="time-slider">
					<slider 
						:value="dailyTimeLimit" 
						@change="changeDailyLimit" 
						min="15" 
						max="240" 
						:step="15" 
						activeColor="#2196f3"
						backgroundColor="#e0e0e0"
						blockSize="28"
					/>
					<view class="slider-labels">
						<text>15分钟</text>
						<text>4小时</text>
					</view>
				</view>
				
				<view class="preset-times">
					<view 
						class="preset-button" 
						v-for="preset in timePresets" 
						:key="preset.value"
						:class="{ active: dailyTimeLimit === preset.value }"
						@click="setTimePreset(preset.value)"
					>
						<text>{{ preset.label }}</text>
					</view>
				</view>
			</view>
			
			<!-- Bedtime Settings -->
			<view class="setting-card" v-if="isControlEnabled">
				<view class="setting-header">
					<text class="setting-title">休息时间</text>
					<switch :checked="isBedtimeEnabled" @change="toggleBedtime" color="#2196f3" />
				</view>
				
				<view class="setting-description">
					<text>设置时间段，在此期间禁止使用应用</text>
				</view>
				
				<view class="bedtime-settings" v-if="isBedtimeEnabled">
					<view class="time-row">
						<text class="time-label">开始时间</text>
						<view class="time-picker" @click="showTimePicker('bedtimeStart')">
							<text>{{ bedtimeStart }}</text>
							<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
						</view>
					</view>
					
					<view class="time-row">
						<text class="time-label">结束时间</text>
						<view class="time-picker" @click="showTimePicker('bedtimeEnd')">
							<text>{{ bedtimeEnd }}</text>
							<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
						</view>
					</view>
					
					<view class="days-selector">
						<text class="days-label">生效日期</text>
						<view class="days-buttons">
							<view 
								class="day-button" 
								v-for="(day, index) in weekDays" 
								:key="index"
								:class="{ active: bedtimeDays.includes(index) }"
								@click="toggleBedtimeDay(index)"
							>
								<text>{{ day }}</text>
							</view>
						</view>
					</view>
				</view>
			</view>
			
			<!-- Break Reminder -->
			<view class="setting-card" v-if="isControlEnabled">
				<view class="setting-header">
					<text class="setting-title">休息提醒</text>
					<switch :checked="isBreakEnabled" @change="toggleBreak" color="#2196f3" />
				</view>
				
				<view class="setting-description">
					<text>定时提醒孩子休息，保护眼睛健康</text>
				</view>
				
				<view class="break-settings" v-if="isBreakEnabled">
					<view class="break-selector">
						<text class="break-label">提醒间隔</text>
						<view class="interval-selector">
							<view 
								class="interval-option" 
								v-for="option in breakIntervals" 
								:key="option.value"
								:class="{ active: breakInterval === option.value }"
								@click="setBreakInterval(option.value)"
							>
								<text>{{ option.label }}</text>
							</view>
						</view>
					</view>
					
					<view class="break-selector">
						<text class="break-label">休息时长</text>
						<view class="interval-selector">
							<view 
								class="interval-option" 
								v-for="option in breakDurations" 
								:key="option.value"
								:class="{ active: breakDuration === option.value }"
								@click="setBreakDuration(option.value)"
							>
								<text>{{ option.label }}</text>
							</view>
						</view>
					</view>
				</view>
			</view>
			
			<!-- Bonus Time -->
			<view class="setting-card" v-if="isControlEnabled">
				<view class="setting-header">
					<text class="setting-title">奖励时间</text>
					<switch :checked="isBonusEnabled" @change="toggleBonus" color="#2196f3" />
				</view>
				
				<view class="setting-description">
					<text>可向孩子提供额外的使用时间作为奖励</text>
				</view>
				
				<view class="bonus-settings" v-if="isBonusEnabled">
					<view class="bonus-buttons">
						<view 
							class="bonus-button" 
							v-for="bonus in bonusOptions" 
							:key="bonus.value"
							@click="grantBonusTime(bonus.value)"
						>
							<text>+{{ bonus.label }}</text>
						</view>
					</view>
					
					<view class="bonus-history">
						<text class="history-title">最近奖励</text>
						<view class="history-item" v-for="(item, index) in bonusHistory" :key="index">
							<view class="history-info">
								<text class="history-time">{{ item.date }}</text>
								<text class="history-amount">+{{ item.minutes }}分钟</text>
							</view>
							<text class="history-reason">{{ item.reason }}</text>
						</view>
					</view>
				</view>
			</view>
			
			<!-- Save Button -->
			<view class="save-section">
				<button class="save-button" @click="saveSettings">保存设置</button>
			</view>
		</scroll-view>
		
		<!-- Time Picker Modal -->
		<view class="modal-overlay" v-if="showTimePickerModal">
			<view class="modal-content">
				<view class="modal-header">
					<text class="modal-title">选择时间</text>
					<view class="modal-close" @click="closeTimePicker">
						<text>×</text>
					</view>
				</view>
				
				<view class="time-picker-container">
					<picker
						mode="time"
						:value="currentTimeSelection"
						@change="onTimeSelected"
					>
						<view class="picker-view">{{ currentTimeSelection }}</view>
					</picker>
				</view>
				
				<view class="modal-buttons">
					<button class="cancel-button" @click="closeTimePicker">取消</button>
					<button class="confirm-button" @click="confirmTimePicker">确认</button>
				</view>
			</view>
		</view>
		
		<!-- Bonus Time Modal -->
		<view class="modal-overlay" v-if="showBonusModal">
			<view class="modal-content">
				<view class="modal-header">
					<text class="modal-title">奖励{{ bonusMinutes }}分钟使用时间</text>
					<view class="modal-close" @click="closeBonusModal">
						<text>×</text>
					</view>
				</view>
				
				<view class="bonus-modal-content">
					<text class="bonus-instruction">请输入奖励原因（选填）：</text>
					<input 
						type="text" 
						v-model="bonusReason" 
						placeholder="例如：完成作业、表现好等"
						class="bonus-input"
					/>
				</view>
				
				<view class="modal-buttons">
					<button class="cancel-button" @click="closeBonusModal">取消</button>
					<button class="confirm-button" @click="confirmBonusTime">确认</button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			isControlEnabled: true,
			
			// Daily time limit
			dailyTimeLimit: 120,
			maxDailyLimit: 240,
			timePresets: [
				{ label: '30分钟', value: 30 },
				{ label: '1小时', value: 60 },
				{ label: '2小时', value: 120 },
				{ label: '3小时', value: 180 }
			],
			
			// Weekly usage data
			weeklyUsage: [
				{ label: '一', minutes: 85 },
				{ label: '二', minutes: 120 },
				{ label: '三', minutes: 45 },
				{ label: '四', minutes: 150 },
				{ label: '五', minutes: 95 },
				{ label: '六', minutes: 180 },
				{ label: '日', minutes: 60 }
			],
			
			// Bedtime settings
			isBedtimeEnabled: true,
			bedtimeStart: '21:00',
			bedtimeEnd: '07:00',
			bedtimeDays: [0, 1, 2, 3, 4], // Monday to Friday
			weekDays: ['一', '二', '三', '四', '五', '六', '日'],
			
			// Break reminders
			isBreakEnabled: true,
			breakInterval: 30,
			breakDuration: 5,
			breakIntervals: [
				{ label: '20分钟', value: 20 },
				{ label: '30分钟', value: 30 },
				{ label: '45分钟', value: 45 },
				{ label: '60分钟', value: 60 }
			],
			breakDurations: [
				{ label: '3分钟', value: 3 },
				{ label: '5分钟', value: 5 },
				{ label: '10分钟', value: 10 },
				{ label: '15分钟', value: 15 }
			],
			
			// Bonus time
			isBonusEnabled: true,
			bonusOptions: [
				{ label: '15分钟', value: 15 },
				{ label: '30分钟', value: 30 },
				{ label: '45分钟', value: 45 },
				{ label: '60分钟', value: 60 }
			],
			bonusHistory: [
				{ date: '2023-12-01', minutes: 30, reason: '完成所有家庭作业' },
				{ date: '2023-11-28', minutes: 15, reason: '数学测验得高分' },
				{ date: '2023-11-25', minutes: 45, reason: '生日特别奖励' }
			],
			
			// Time picker modal
			showTimePickerModal: false,
			currentTimeSelection: '21:00',
			timePickerTarget: '',
			
			// Bonus modal
			showBonusModal: false,
			bonusMinutes: 0,
			bonusReason: ''
		}
	},
	methods: {
		navigateBack() {
			uni.navigateBack();
		},
		
		// Control toggle
		toggleControl(e) {
			this.isControlEnabled = e.detail.value;
		},
		
		// Daily time limit
		changeDailyLimit(e) {
			this.dailyTimeLimit = e.detail.value;
		},
		
		setTimePreset(value) {
			this.dailyTimeLimit = value;
		},
		
		// Weekly report
		showWeeklyReport() {
			uni.navigateTo({
				url: '/pages/settings/usage-report'
			});
		},
		
		// Bedtime settings
		toggleBedtime(e) {
			this.isBedtimeEnabled = e.detail.value;
		},
		
		showTimePicker(target) {
			this.timePickerTarget = target;
			this.currentTimeSelection = target === 'bedtimeStart' ? this.bedtimeStart : this.bedtimeEnd;
			this.showTimePickerModal = true;
		},
		
		closeTimePicker() {
			this.showTimePickerModal = false;
		},
		
		onTimeSelected(e) {
			this.currentTimeSelection = e.detail.value;
		},
		
		confirmTimePicker() {
			if (this.timePickerTarget === 'bedtimeStart') {
				this.bedtimeStart = this.currentTimeSelection;
			} else {
				this.bedtimeEnd = this.currentTimeSelection;
			}
			this.showTimePickerModal = false;
		},
		
		toggleBedtimeDay(index) {
			const position = this.bedtimeDays.indexOf(index);
			if (position === -1) {
				this.bedtimeDays.push(index);
			} else {
				this.bedtimeDays.splice(position, 1);
			}
		},
		
		// Break reminders
		toggleBreak(e) {
			this.isBreakEnabled = e.detail.value;
		},
		
		setBreakInterval(value) {
			this.breakInterval = value;
		},
		
		setBreakDuration(value) {
			this.breakDuration = value;
		},
		
		// Bonus time
		toggleBonus(e) {
			this.isBonusEnabled = e.detail.value;
		},
		
		grantBonusTime(minutes) {
			this.bonusMinutes = minutes;
			this.bonusReason = '';
			this.showBonusModal = true;
		},
		
		closeBonusModal() {
			this.showBonusModal = false;
		},
		
		confirmBonusTime() {
			// Add bonus time to history
			const today = new Date();
			const formattedDate = `${today.getFullYear()}-${String(today.getMonth() + 1).padStart(2, '0')}-${String(today.getDate()).padStart(2, '0')}`;
			
			this.bonusHistory.unshift({
				date: formattedDate,
				minutes: this.bonusMinutes,
				reason: this.bonusReason || '家长奖励'
			});
			
			uni.showToast({
				title: `已添加${this.bonusMinutes}分钟奖励时间`,
				icon: 'success'
			});
			
			this.showBonusModal = false;
		},
		
		// Save settings
		saveSettings() {
			uni.showLoading({
				title: '保存中...'
			});
			
			setTimeout(() => {
				uni.hideLoading();
				uni.showToast({
					title: '设置已保存',
					icon: 'success'
				});
				
				setTimeout(() => {
					uni.navigateBack();
				}, 1500);
			}, 1000);
		}
	}
}
</script>

<style>
.usage-control-container {
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

.control-content {
	padding: 30rpx;
	height: calc(100vh - 150rpx);
}

.control-card, .stats-card, .setting-card {
	background-color: #fff;
	border-radius: 16rpx;
	padding: 30rpx;
	margin-bottom: 30rpx;
	box-shadow: 0 2rpx 10rpx rgba(0,0,0,0.05);
}

.control-header, .stats-header, .setting-header {
	display: flex;
	justify-content: space-between;
	align-items: center;
	margin-bottom: 20rpx;
}

.control-title, .stats-title, .setting-title {
	font-size: 32rpx;
	font-weight: bold;
	color: #333;
}

.control-description, .setting-description {
	font-size: 26rpx;
	color: #666;
	margin-bottom: 20rpx;
}

/* Weekly Stats */
.view-details {
	font-size: 26rpx;
	color: #2196f3;
}

.weekly-chart {
	height: 320rpx;
	position: relative;
	margin: 40rpx 0 20rpx;
}

.chart-bars {
	display: flex;
	justify-content: space-between;
	height: 100%;
}

.day-column {
	display: flex;
	flex-direction: column;
	align-items: center;
	width: 12%;
}

.usage-bar-container {
	flex: 1;
	width: 100%;
	display: flex;
	align-items: flex-end;
	justify-content: center;
	padding-bottom: 10rpx;
}

.usage-bar {
	width: 60%;
	position: relative;
}

.bar-segment {
	width: 100%;
	position: absolute;
	bottom: 0;
	border-radius: 8rpx;
}

.bar-segment.over-limit {
	background-color: #ff6b6b;
}

.day-label {
	font-size: 24rpx;
	color: #666;
	margin-bottom: 6rpx;
}

.usage-time {
	font-size: 22rpx;
	color: #999;
}

.limit-line {
	position: absolute;
	left: 0;
	right: 0;
	height: 2rpx;
	background-color: #e74c3c;
}

.limit-label {
	position: absolute;
	right: 10rpx;
	top: -30rpx;
	font-size: 22rpx;
	color: #e74c3c;
}

/* Daily Time Limit */
.time-display {
	font-size: 28rpx;
	color: #2196f3;
	font-weight: bold;
}

.time-slider {
	margin: 30rpx 0;
}

.slider-labels {
	display: flex;
	justify-content: space-between;
	margin-top: 10rpx;
}

.slider-labels text {
	font-size: 24rpx;
	color: #999;
}

.preset-times {
	display: flex;
	justify-content: space-between;
	margin-top: 20rpx;
}

.preset-button {
	background-color: #f0f0f0;
	padding: 16rpx 0;
	border-radius: 30rpx;
	width: 22%;
	text-align: center;
}

.preset-button.active {
	background-color: #2196f3;
}

.preset-button text {
	font-size: 26rpx;
	color: #666;
}

.preset-button.active text {
	color: white;
}

/* Bedtime Settings */
.time-row {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 20rpx 0;
	border-bottom: 1rpx solid #f0f0f0;
}

.time-label {
	font-size: 28rpx;
	color: #333;
}

.time-picker {
	display: flex;
	align-items: center;
}

.time-picker text {
	font-size: 28rpx;
	color: #2196f3;
	margin-right: 10rpx;
}

.arrow-icon {
	width: 24rpx;
	height: 24rpx;
}

.days-selector {
	padding: 20rpx 0;
}

.days-label {
	font-size: 28rpx;
	color: #333;
	display: block;
	margin-bottom: 20rpx;
}

.days-buttons {
	display: flex;
	justify-content: space-between;
}

.day-button {
	width: 60rpx;
	height: 60rpx;
	border-radius: 30rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	background-color: #f0f0f0;
}

.day-button text {
	font-size: 24rpx;
	color: #666;
}

.day-button.active {
	background-color: #2196f3;
}

.day-button.active text {
	color: white;
}

/* Break Reminder Settings */
.break-selector {
	margin-bottom: 30rpx;
}

.break-label {
	font-size: 28rpx;
	color: #333;
	display: block;
	margin-bottom: 20rpx;
}

.interval-selector {
	display: flex;
	justify-content: space-between;
}

.interval-option {
	background-color: #f0f0f0;
	padding: 16rpx 0;
	border-radius: 30rpx;
	width: 22%;
	text-align: center;
}

.interval-option.active {
	background-color: #2196f3;
}

.interval-option text {
	font-size: 26rpx;
	color: #666;
}

.interval-option.active text {
	color: white;
}

/* Bonus Time Settings */
.bonus-buttons {
	display: flex;
	justify-content: space-between;
	margin-bottom: 30rpx;
}

.bonus-button {
	background-color: #f0f0f0;
	padding: 16rpx 0;
	border-radius: 30rpx;
	width: 22%;
	text-align: center;
}

.bonus-button text {
	font-size: 26rpx;
	color: #2196f3;
	font-weight: bold;
}

.bonus-history {
	background-color: #f9f9f9;
	border-radius: 16rpx;
	padding: 20rpx;
}

.history-title {
	font-size: 28rpx;
	color: #333;
	font-weight: bold;
	margin-bottom: 20rpx;
	display: block;
}

.history-item {
	padding: 15rpx 0;
	border-bottom: 1rpx solid #f0f0f0;
}

.history-item:last-child {
	border-bottom: none;
}

.history-info {
	display: flex;
	justify-content: space-between;
	margin-bottom: 6rpx;
}

.history-time {
	font-size: 24rpx;
	color: #666;
}

.history-amount {
	font-size: 24rpx;
	color: #2196f3;
	font-weight: bold;
}

.history-reason {
	font-size: 24rpx;
	color: #999;
}

/* Save Button */
.save-section {
	margin: 30rpx 0;
}

.save-button {
	background-color: #2196f3;
	color: white;
	height: 90rpx;
	line-height: 90rpx;
	font-size: 32rpx;
	border-radius: 45rpx;
}

/* Modal */
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

.time-picker-container {
	margin-bottom: 30rpx;
}

.picker-view {
	text-align: center;
	font-size: 36rpx;
	color: #2196f3;
	padding: 20rpx 0;
}

.bonus-modal-content {
	margin-bottom: 30rpx;
}

.bonus-instruction {
	font-size: 28rpx;
	color: #333;
	margin-bottom: 20rpx;
	display: block;
}

.bonus-input {
	width: 100%;
	height: 80rpx;
	border: 1rpx solid #eee;
	border-radius: 8rpx;
	padding: 0 20rpx;
	font-size: 28rpx;
	box-sizing: border-box;
}

.modal-buttons {
	display: flex;
	justify-content: space-between;
}

.cancel-button, .confirm-button {
	width: 48%;
	height: 80rpx;
	line-height: 80rpx;
	font-size: 28rpx;
	border-radius: 40rpx;
}

.cancel-button {
	background-color: #f5f5f5;
	color: #666;
}

.confirm-button {
	background-color: #2196f3;
	color: white;
}
</style> 