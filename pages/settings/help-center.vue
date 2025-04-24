<template>
	<view class="help-center">
		<!-- Header -->
		<view class="page-header">
			<view class="back-button" @click="navigateBack">
				<text class="iconfont icon-left"></text>
			</view>
			<text class="page-title">帮助中心</text>
			<view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
		</view>
		
		<!-- Search Bar -->
		<view class="search-container">
			<view class="search-bar">
				<text class="iconfont icon-search"></text>
				<input type="text" placeholder="搜索问题" v-model="searchQuery" @input="handleSearch" />
				<text class="clear-icon" v-if="searchQuery" @click="clearSearch">×</text>
			</view>
		</view>
		
		<!-- Content -->
		<scroll-view scroll-y class="help-content">
			<block v-if="searchActive && filteredQuestions.length > 0">
				<view class="section-title">搜索结果</view>
				<view class="question-item" v-for="(item, index) in filteredQuestions" :key="index" @click="toggleQuestion(item)">
					<view class="question-header">
						<text class="question-text">{{ item.question }}</text>
						<text class="toggle-icon">{{ item.expanded ? '−' : '+' }}</text>
					</view>
					<view class="answer-container" v-if="item.expanded">
						<text class="answer-text">{{ item.answer }}</text>
					</view>
				</view>
			</block>
			
			<block v-else-if="searchActive && filteredQuestions.length === 0">
				<view class="no-results">
					<image src="/static/images/no-results.png" mode="aspectFit" class="no-results-image"></image>
					<text class="no-results-text">没有找到相关问题</text>
					<text class="suggestion-text">您可以尝试:</text>
					<text class="suggestion-item">• 使用不同的关键词</text>
					<text class="suggestion-item">• 查看下方的常见问题</text>
					<text class="suggestion-item">• 联系客服获取帮助</text>
				</view>
			</block>
			
			<block v-else>
				<!-- Common Questions -->
				<view class="section-title">常见问题</view>
				<view class="question-item" v-for="(item, index) in commonQuestions" :key="index" @click="toggleQuestion(item)">
					<view class="question-header">
						<text class="question-text">{{ item.question }}</text>
						<text class="toggle-icon">{{ item.expanded ? '−' : '+' }}</text>
					</view>
					<view class="answer-container" v-if="item.expanded">
						<text class="answer-text">{{ item.answer }}</text>
					</view>
				</view>
				
				<!-- Account & Login -->
				<view class="section-title">账号与登录</view>
				<view class="question-item" v-for="(item, index) in accountQuestions" :key="index" @click="toggleQuestion(item)">
					<view class="question-header">
						<text class="question-text">{{ item.question }}</text>
						<text class="toggle-icon">{{ item.expanded ? '−' : '+' }}</text>
					</view>
					<view class="answer-container" v-if="item.expanded">
						<text class="answer-text">{{ item.answer }}</text>
					</view>
				</view>
				
				<!-- Content & Learning -->
				<view class="section-title">内容与学习</view>
				<view class="question-item" v-for="(item, index) in contentQuestions" :key="index" @click="toggleQuestion(item)">
					<view class="question-header">
						<text class="question-text">{{ item.question }}</text>
						<text class="toggle-icon">{{ item.expanded ? '−' : '+' }}</text>
					</view>
					<view class="answer-container" v-if="item.expanded">
						<text class="answer-text">{{ item.answer }}</text>
					</view>
				</view>
				
				<!-- Parental Controls -->
				<view class="section-title">家长控制</view>
				<view class="question-item" v-for="(item, index) in parentalQuestions" :key="index" @click="toggleQuestion(item)">
					<view class="question-header">
						<text class="question-text">{{ item.question }}</text>
						<text class="toggle-icon">{{ item.expanded ? '−' : '+' }}</text>
					</view>
					<view class="answer-container" v-if="item.expanded">
						<text class="answer-text">{{ item.answer }}</text>
					</view>
				</view>
				
				<!-- Technical Issues -->
				<view class="section-title">技术问题</view>
				<view class="question-item" v-for="(item, index) in technicalQuestions" :key="index" @click="toggleQuestion(item)">
					<view class="question-header">
						<text class="question-text">{{ item.question }}</text>
						<text class="toggle-icon">{{ item.expanded ? '−' : '+' }}</text>
					</view>
					<view class="answer-container" v-if="item.expanded">
						<text class="answer-text">{{ item.answer }}</text>
					</view>
				</view>
			</block>
			
			<!-- Contact Support -->
			<view class="contact-support">
				<view class="divider">
					<text class="divider-text">还有其他问题？</text>
				</view>
				<button class="contact-button" @click="contactCustomerService">联系客服</button>
			</view>
		</scroll-view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			searchQuery: '',
			searchActive: false,
			filteredQuestions: [],
			allQuestions: [],
			commonQuestions: [
				{
					question: '小智App是什么？',
					answer: '小智App是一款专为5-12岁儿童设计的AI伴学应用，通过人工智能技术提供个性化的学习内容推荐、AI互动对话、学习管理等功能，帮助儿童在愉快的环境中学习和成长。',
					expanded: false
				},
				{
					question: '如何开始使用小智App？',
					answer: '下载并安装小智App后，您需要注册一个账号。注册完成后，您可以为孩子创建一个专属的学习档案，包括年龄、兴趣等信息，这将帮助我们为孩子提供更加个性化的学习体验。',
					expanded: false
				},
				{
					question: '小智App是否免费？',
					answer: '小智App提供基础版和会员版。基础版是免费的，包含基本的学习功能；会员版则提供更多高级功能，如更多学习内容、个性化学习计划等。您可以在应用内查看详细的会员权益和价格。',
					expanded: false
				}
			],
			accountQuestions: [
				{
					question: '如何重置密码？',
					answer: '在登录页面，点击"忘记密码"，然后按照提示输入您的注册手机号或邮箱，我们会向您发送一个验证码。输入验证码后，您可以设置新的密码。',
					expanded: false
				},
				{
					question: '如何修改账号信息？',
					answer: '在个人主页中点击"编辑资料"，您可以修改昵称、头像等个人信息。如需修改手机号或邮箱，请在"设置"中的"账号与安全"中进行操作。',
					expanded: false
				},
				{
					question: '如何删除我的账号？',
					answer: '请在"设置"中找到"账号与安全"，然后点击"注销账号"。注意，账号注销后，所有数据将被永久删除，且无法恢复。',
					expanded: false
				}
			],
			contentQuestions: [
				{
					question: '如何下载内容以供离线使用？',
					answer: '在内容详情页面，点击"下载"按钮即可。下载的内容可在"我的下载"中查看。请注意，某些内容仅对会员用户开放下载功能。',
					expanded: false
				},
				{
					question: '内容推荐是如何工作的？',
					answer: '我们的推荐系统基于孩子的年龄、兴趣、学习记录以及AI技术分析，为孩子推荐最适合的学习内容。使用越多，推荐越精准。',
					expanded: false
				},
				{
					question: '如何给内容添加收藏？',
					answer: '在内容详情页面，点击右上角的心形图标即可将内容添加到收藏。您可以在"我的收藏"中查看所有收藏的内容。',
					expanded: false
				}
			],
			parentalQuestions: [
				{
					question: '如何设置使用时间限制？',
					answer: '在"设置"中找到"家长控制"，然后点击"使用控制"。您可以设置每日使用时长限制、休息提醒以及使用时间段限制等。',
					expanded: false
				},
				{
					question: '如何过滤不适合的内容？',
					answer: '在"设置"中找到"家长控制"，然后点击"内容过滤"。您可以根据孩子的年龄和您的偏好设置内容过滤级别，也可以手动禁止特定类型的内容。',
					expanded: false
				},
				{
					question: '如何查看孩子的学习报告？',
					answer: '在"家长中心"中，您可以查看孩子的学习时长、学习内容、学习进度等详细报告。系统还会根据数据生成学习建议，帮助孩子更好地学习。',
					expanded: false
				}
			],
			technicalQuestions: [
				{
					question: 'App闪退怎么办？',
					answer: '请尝试以下方法：1) 确保您的App是最新版本；2) 重启设备；3) 清除App缓存；4) 如果问题仍然存在，请尝试卸载并重新安装App。',
					expanded: false
				},
				{
					question: '为什么视频播放卡顿？',
					answer: '视频卡顿可能是由网络连接不稳定或设备性能问题导致的。请尝试连接到更稳定的Wi-Fi网络，或降低视频质量设置。您也可以尝试关闭其他应用以释放设备资源。',
					expanded: false
				},
				{
					question: '如何同步数据到新设备？',
					answer: '只要使用同一账号登录，您的数据将自动同步到新设备。如遇同步问题，请确保两台设备都已连接网络，并尝试在"设置"中手动触发同步。',
					expanded: false
				}
			]
		}
	},
	created() {
		// Combine all questions for search functionality
		this.allQuestions = [
			...this.commonQuestions,
			...this.accountQuestions,
			...this.contentQuestions,
			...this.parentalQuestions,
			...this.technicalQuestions
		]
	},
	methods: {
		navigateBack() {
			uni.navigateBack()
		},
		
		handleSearch() {
			if (this.searchQuery.trim()) {
				this.searchActive = true
				const query = this.searchQuery.toLowerCase()
				this.filteredQuestions = this.allQuestions.filter(item => {
					return item.question.toLowerCase().includes(query) || 
					       item.answer.toLowerCase().includes(query)
				}).map(item => ({...item, expanded: false}))
			} else {
				this.searchActive = false
				this.filteredQuestions = []
			}
		},
		
		clearSearch() {
			this.searchQuery = ''
			this.searchActive = false
			this.filteredQuestions = []
		},
		
		toggleQuestion(item) {
			item.expanded = !item.expanded
		},
		
		contactCustomerService() {
			// Options for customer service contact
			uni.showActionSheet({
				itemList: ['在线客服', '电话客服', '发送邮件'],
				success: (res) => {
					switch(res.tapIndex) {
						case 0: // Online customer service
							uni.navigateTo({
								url: '/pages/customer-service/chat'
							})
							break
						case 1: // Phone customer service
							uni.makePhoneCall({
								phoneNumber: '400-123-4567',
								fail: () => {
									uni.showToast({
										title: '拨打电话失败',
										icon: 'none'
									})
								}
							})
							break
						case 2: // Email customer service
							// This would typically use system email capabilities
							// For simplicity, we'll just show a modal with the email address
							uni.showModal({
								title: '发送邮件',
								content: '请发送邮件至 support@xiaozhi.com 获取帮助',
								showCancel: false,
								confirmText: '确定'
							})
							break
					}
				}
			})
		}
	}
}
</script>

<style>
.help-center {
	display: flex;
	flex-direction: column;
	height: 100vh;
	background-color: #fff;
}

.page-header {
	display: flex;
	align-items: center;
	justify-content: space-between;
	padding: 30rpx;
	background-color: #fff;
	border-bottom: 1rpx solid #f5f5f5;
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

.search-container {
	padding: 20rpx 30rpx;
	background-color: #fff;
}

.search-bar {
	display: flex;
	align-items: center;
	background-color: #f8f9fa;
	border-radius: 100rpx;
	padding: 10rpx 20rpx;
	position: relative;
}

.icon-search {
	margin-right: 10rpx;
	color: #6c757d;
	font-size: 28rpx;
}

.search-bar input {
	flex: 1;
	height: 60rpx;
	font-size: 28rpx;
}

.clear-icon {
	color: #6c757d;
	font-size: 36rpx;
	padding: 0 10rpx;
}

.help-content {
	flex: 1;
	padding: 30rpx;
}

.section-title {
	font-size: 32rpx;
	font-weight: 600;
	margin: 40rpx 0 20rpx 0;
	color: #212529;
}

.section-title:first-child {
	margin-top: 0;
}

.question-item {
	background-color: #f8f9fa;
	border-radius: 16rpx;
	margin-bottom: 20rpx;
	overflow: hidden;
}

.question-header {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 20rpx;
}

.question-text {
	font-size: 28rpx;
	font-weight: 500;
	color: #212529;
	flex: 1;
}

.toggle-icon {
	font-size: 36rpx;
	color: #6c757d;
	margin-left: 20rpx;
}

.answer-container {
	padding: 0 20rpx 20rpx 20rpx;
	border-top: 1rpx solid #e9ecef;
}

.answer-text {
	font-size: 26rpx;
	color: #495057;
	line-height: 1.6;
}

.no-results {
	display: flex;
	flex-direction: column;
	align-items: center;
	padding: 60rpx 0;
}

.no-results-image {
	width: 200rpx;
	height: 200rpx;
	margin-bottom: 30rpx;
}

.no-results-text {
	font-size: 30rpx;
	font-weight: 500;
	color: #212529;
	margin-bottom: 20rpx;
}

.suggestion-text {
	font-size: 26rpx;
	color: #6c757d;
	margin-bottom: 10rpx;
}

.suggestion-item {
	font-size: 26rpx;
	color: #6c757d;
	line-height: 1.6;
}

.contact-support {
	margin: 60rpx 0 40rpx 0;
	display: flex;
	flex-direction: column;
	align-items: center;
}

.divider {
	position: relative;
	width: 100%;
	text-align: center;
	margin-bottom: 30rpx;
}

.divider:before, .divider:after {
	content: '';
	position: absolute;
	top: 50%;
	width: 30%;
	height: 1rpx;
	background-color: #e9ecef;
}

.divider:before {
	left: 0;
}

.divider:after {
	right: 0;
}

.divider-text {
	display: inline-block;
	padding: 0 20rpx;
	font-size: 26rpx;
	color: #6c757d;
	background-color: #fff;
	position: relative;
	z-index: 1;
}

.contact-button {
	width: 60%;
	height: 80rpx;
	line-height: 80rpx;
	background-color: #f8f9fa;
	color: #212529;
	border-radius: 100rpx;
	font-size: 28rpx;
	border: none;
}
</style>