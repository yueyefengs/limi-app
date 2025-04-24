<template>
	<view class="feedback-container">
		<!-- Header -->
		<view class="page-header">
			<view class="back-button" @click="navigateBack">
				<text class="iconfont icon-left"></text>
			</view>
			<text class="page-title">意见反馈</text>
			<view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
		</view>
		
		<!-- Feedback Form -->
		<view class="feedback-form">
			<!-- Feedback Type -->
			<view class="form-section">
				<view class="section-header">
					<text class="section-title">反馈类型</text>
					<text class="required-marker">*</text>
				</view>
				<view class="type-options">
					<view 
						v-for="(type, index) in feedbackTypes" 
						:key="index" 
						class="type-item" 
						:class="{ 'type-selected': selectedType === type.value }"
						@click="selectType(type.value)"
					>
						<text class="type-icon" :class="type.icon"></text>
						<text class="type-name">{{ type.name }}</text>
					</view>
				</view>
			</view>
			
			<!-- Feedback Content -->
			<view class="form-section">
				<view class="section-header">
					<text class="section-title">反馈内容</text>
					<text class="required-marker">*</text>
				</view>
				<view class="textarea-container">
					<textarea 
						v-model="feedbackContent" 
						placeholder="请详细描述您遇到的问题或建议，以便我们更好地为您解决" 
						class="feedback-textarea"
						:maxlength="500"
					/>
					<text class="character-count">{{ feedbackContent.length }}/500</text>
				</view>
			</view>
			
			<!-- Contact Information -->
			<view class="form-section">
				<view class="section-header">
					<text class="section-title">联系方式</text>
					<text class="optional-marker">(选填)</text>
				</view>
				<view class="contact-field">
					<view class="input-label">Email</view>
					<input 
						type="text" 
						v-model="contactEmail" 
						placeholder="请输入您的邮箱地址，便于我们回复" 
						class="contact-input"
					/>
				</view>
				<view class="contact-field">
					<view class="input-label">手机号码</view>
					<input 
						type="number" 
						v-model="contactPhone" 
						placeholder="请输入您的手机号码" 
						class="contact-input"
					/>
				</view>
			</view>
			
			<!-- Screenshots/Attachments -->
			<view class="form-section">
				<view class="section-header">
					<text class="section-title">附件/截图</text>
					<text class="optional-marker">(选填，最多4张)</text>
				</view>
				<view class="attachment-container">
					<view 
						v-for="(img, index) in attachments" 
						:key="index" 
						class="attachment-item"
					>
						<image :src="img" class="attachment-img" mode="aspectFill" />
						<view class="delete-btn" @click="removeAttachment(index)">
							<text class="iconfont icon-close"></text>
						</view>
					</view>
					
					<view class="add-attachment" @click="addAttachment" v-if="attachments.length < 4">
						<text class="iconfont icon-add"></text>
						<text class="add-text">添加截图</text>
					</view>
				</view>
			</view>
			
			<!-- Device Information -->
			<view class="form-section">
				<view class="section-header">
					<text class="section-title">设备信息</text>
				</view>
				<view class="device-info">
					<text>系统：{{ deviceInfo.platform }} {{ deviceInfo.system }}</text>
					<text>应用版本：{{ deviceInfo.appVersion }}</text>
					<text>设备型号：{{ deviceInfo.model }}</text>
					<text>网络类型：{{ deviceInfo.networkType }}</text>
				</view>
				<view class="send-device-info">
					<switch 
						:checked="sendDeviceInfo" 
						@change="toggleSendDeviceInfo" 
						color="#4a90e2"
					/>
					<text class="toggle-label">发送设备信息（有助于问题定位）</text>
				</view>
			</view>
			
			<!-- Submit Button -->
			<button 
				class="submit-button" 
				:disabled="!isFormValid" 
				:class="{ 'button-disabled': !isFormValid }"
				@click="submitFeedback"
			>
				提交反馈
			</button>
			
			<!-- Common Feedback Submissions -->
			<view class="common-feedback-section">
				<view class="section-header">
					<text class="section-title">热门反馈</text>
				</view>
				<view class="common-feedback-list">
					<view 
						v-for="(item, index) in commonFeedback" 
						:key="index" 
						class="common-feedback-item"
						@click="viewFeedbackDetail(item)"
					>
						<view class="feedback-item-content">
							<text class="feedback-item-tag" :class="'tag-' + item.type">{{ getFeedbackTypeName(item.type) }}</text>
							<text class="feedback-item-title">{{ item.title }}</text>
						</view>
						<view class="feedback-item-status" :class="'status-' + item.status">
							{{ getStatusName(item.status) }}
						</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			feedbackTypes: [
				{ name: '功能建议', value: 'suggestion', icon: 'iconfont icon-lightbulb' },
				{ name: '程序问题', value: 'bug', icon: 'iconfont icon-bug' },
				{ name: '内容相关', value: 'content', icon: 'iconfont icon-file-text' },
				{ name: '账号问题', value: 'account', icon: 'iconfont icon-user' },
				{ name: '其他', value: 'other', icon: 'iconfont icon-more' }
			],
			selectedType: '',
			feedbackContent: '',
			contactEmail: '',
			contactPhone: '',
			attachments: [],
			sendDeviceInfo: true,
			deviceInfo: {
				platform: 'Android',
				system: '10',
				appVersion: 'v1.0.0',
				model: 'Unknown',
				networkType: 'wifi'
			},
			commonFeedback: [
				{
					title: '建议增加更多互动游戏内容',
					type: 'suggestion',
					status: 'planned',
					id: '1001'
				},
				{
					title: '视频播放卡顿问题',
					type: 'bug',
					status: 'processing',
					id: '1002'
				},
				{
					title: '部分英语内容发音不准确',
					type: 'content',
					status: 'completed',
					id: '1003'
				},
				{
					title: '家长模式密码重置功能',
					type: 'suggestion',
					status: 'planned',
					id: '1004'
				}
			]
		}
	},
	computed: {
		isFormValid() {
			return this.selectedType && this.feedbackContent.trim().length > 0
		}
	},
	onLoad() {
		this.getDeviceInfo()
	},
	methods: {
		navigateBack() {
			uni.navigateBack()
		},
		selectType(type) {
			this.selectedType = type
		},
		addAttachment() {
			if (this.attachments.length >= 4) {
				uni.showToast({
					title: '最多只能添加4张图片',
					icon: 'none'
				})
				return
			}
			
			uni.chooseImage({
				count: 1,
				sizeType: ['compressed'],
				sourceType: ['album', 'camera'],
				success: (res) => {
					this.attachments.push(res.tempFilePaths[0])
				}
			})
		},
		removeAttachment(index) {
			this.attachments.splice(index, 1)
		},
		toggleSendDeviceInfo(e) {
			this.sendDeviceInfo = e.detail.value
		},
		getDeviceInfo() {
			try {
				const systemInfo = uni.getSystemInfoSync()
				this.deviceInfo = {
					platform: systemInfo.platform,
					system: systemInfo.system,
					appVersion: 'v1.0.0', // In real app, get this from app config
					model: systemInfo.model,
					networkType: '获取中...'
				}
				
				// Get network type
				uni.getNetworkType({
					success: (res) => {
						this.deviceInfo.networkType = res.networkType
					}
				})
			} catch (e) {
				console.error('Failed to get device info', e)
			}
		},
		submitFeedback() {
			if (!this.isFormValid) {
				return
			}
			
			uni.showLoading({
				title: '提交中...'
			})
			
			// Simulate API call
			setTimeout(() => {
				uni.hideLoading()
				
				uni.showToast({
					title: '提交成功',
					icon: 'success'
				})
				
				// Reset form after successful submission
				setTimeout(() => {
					this.selectedType = ''
					this.feedbackContent = ''
					this.attachments = []
					uni.navigateBack()
				}, 1500)
			}, 2000)
		},
		getFeedbackTypeName(type) {
			const foundType = this.feedbackTypes.find(t => t.value === type)
			return foundType ? foundType.name : '其他'
		},
		getStatusName(status) {
			const statusMap = {
				'planned': '计划中',
				'processing': '处理中',
				'completed': '已完成',
				'rejected': '未采纳'
			}
			return statusMap[status] || '未知'
		},
		viewFeedbackDetail(item) {
			uni.showModal({
				title: '反馈详情',
				content: `${item.title}\n\n状态：${this.getStatusName(item.status)}\n\n我们已收到多位用户的类似反馈，正在积极跟进处理中，感谢您的支持！`,
				showCancel: false,
				confirmText: '我知道了'
			})
		}
	}
}
</script>

<style>
.feedback-container {
	background-color: #f5f5f5;
	min-height: 100vh;
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

.feedback-form {
	padding-bottom: 40rpx;
}

.form-section {
	background-color: #fff;
	margin-top: 20rpx;
	padding: 30rpx;
}

.section-header {
	display: flex;
	align-items: center;
	margin-bottom: 20rpx;
}

.section-title {
	font-size: 32rpx;
	font-weight: 500;
	color: #212529;
}

.required-marker {
	color: #dc3545;
	margin-left: 10rpx;
}

.optional-marker {
	color: #6c757d;
	font-size: 26rpx;
	margin-left: 10rpx;
}

.type-options {
	display: flex;
	flex-wrap: wrap;
	justify-content: space-between;
}

.type-item {
	width: 32%;
	margin-bottom: 20rpx;
	background-color: #f8f9fa;
	border-radius: 10rpx;
	padding: 20rpx 0;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
}

.type-selected {
	background-color: #e6f2ff;
	border: 1rpx solid #4a90e2;
}

.type-icon {
	font-size: 36rpx;
	color: #4a90e2;
	margin-bottom: 10rpx;
}

.type-name {
	font-size: 26rpx;
	color: #212529;
}

.textarea-container {
	position: relative;
	border: 1rpx solid #ced4da;
	border-radius: 10rpx;
	padding: 20rpx;
	background-color: #f8f9fa;
}

.feedback-textarea {
	width: 100%;
	height: 200rpx;
	font-size: 28rpx;
	line-height: 1.5;
}

.character-count {
	position: absolute;
	bottom: 10rpx;
	right: 20rpx;
	font-size: 24rpx;
	color: #6c757d;
}

.contact-field {
	margin-bottom: 20rpx;
}

.input-label {
	font-size: 28rpx;
	color: #212529;
	margin-bottom: 10rpx;
}

.contact-input {
	height: 80rpx;
	border: 1rpx solid #ced4da;
	border-radius: 10rpx;
	padding: 0 20rpx;
	background-color: #f8f9fa;
	font-size: 28rpx;
}

.attachment-container {
	display: flex;
	flex-wrap: wrap;
}

.attachment-item, .add-attachment {
	width: 150rpx;
	height: 150rpx;
	margin-right: 20rpx;
	margin-bottom: 20rpx;
	border-radius: 10rpx;
	overflow: hidden;
	position: relative;
}

.attachment-img {
	width: 100%;
	height: 100%;
}

.delete-btn {
	position: absolute;
	top: 5rpx;
	right: 5rpx;
	width: 40rpx;
	height: 40rpx;
	background-color: rgba(0, 0, 0, 0.5);
	border-radius: 20rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	color: #fff;
}

.add-attachment {
	border: 1rpx dashed #ced4da;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	background-color: #f8f9fa;
}

.add-attachment .iconfont {
	font-size: 40rpx;
	color: #6c757d;
	margin-bottom: 10rpx;
}

.add-text {
	font-size: 24rpx;
	color: #6c757d;
}

.device-info {
	background-color: #f8f9fa;
	border-radius: 10rpx;
	padding: 20rpx;
}

.device-info text {
	display: block;
	font-size: 26rpx;
	color: #6c757d;
	margin-bottom: 10rpx;
}

.send-device-info {
	display: flex;
	align-items: center;
	margin-top: 20rpx;
}

.toggle-label {
	font-size: 28rpx;
	color: #212529;
	margin-left: 20rpx;
}

.submit-button {
	height: 90rpx;
	background-color: #4a90e2;
	color: #fff;
	border-radius: 45rpx;
	margin: 40rpx 30rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	font-size: 32rpx;
	font-weight: 500;
}

.button-disabled {
	background-color: #ced4da;
	color: #6c757d;
}

.common-feedback-section {
	margin: 20rpx 30rpx;
	background-color: #fff;
	border-radius: 20rpx;
	padding: 30rpx;
	box-shadow: 0 4rpx 10rpx rgba(0,0,0,0.05);
}

.common-feedback-list {
	margin-top: 20rpx;
}

.common-feedback-item {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 20rpx 0;
	border-bottom: 1rpx solid #f5f5f5;
}

.common-feedback-item:last-child {
	border-bottom: none;
}

.feedback-item-content {
	flex: 1;
	padding-right: 20rpx;
}

.feedback-item-tag {
	display: inline-block;
	font-size: 24rpx;
	padding: 6rpx 12rpx;
	border-radius: 6rpx;
	margin-right: 10rpx;
}

.tag-suggestion {
	background-color: #e6f2ff;
	color: #4a90e2;
}

.tag-bug {
	background-color: #ffebee;
	color: #dc3545;
}

.tag-content {
	background-color: #e8f5e9;
	color: #28a745;
}

.tag-account {
	background-color: #fff3e0;
	color: #ff9800;
}

.tag-other {
	background-color: #f5f5f5;
	color: #6c757d;
}

.feedback-item-title {
	font-size: 28rpx;
	color: #212529;
}

.feedback-item-status {
	font-size: 24rpx;
	padding: 6rpx 12rpx;
	border-radius: 6rpx;
}

.status-planned {
	background-color: #e6f2ff;
	color: #4a90e2;
}

.status-processing {
	background-color: #fff3e0;
	color: #ff9800;
}

.status-completed {
	background-color: #e8f5e9;
	color: #28a745;
}

.status-rejected {
	background-color: #f5f5f5;
	color: #6c757d;
}
</style> 