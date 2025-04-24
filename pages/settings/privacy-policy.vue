<template>
	<view class="privacy-container">
		<!-- Header -->
		<view class="page-header">
			<view class="back-button" @click="navigateBack">
				<text class="iconfont icon-left"></text>
			</view>
			<text class="page-title">隐私政策</text>
			<view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
		</view>
		
		<scroll-view scroll-y class="privacy-content">
			<view class="last-updated">更新日期：2023年12月1日</view>
			
			<view class="privacy-intro">
				<text class="intro-text">小智App（以下简称"我们"）非常重视您的隐私和个人信息保护。本隐私政策旨在向您说明我们如何收集、使用、存储和共享您的个人信息，以及您享有的相关权利。请您在使用我们的服务前，仔细阅读并充分理解本隐私政策。</text>
			</view>
			
			<view class="section-container">
				<view class="section-title">一、我们收集的信息</view>
				<view class="section-content">
					<view class="content-item">
						<text class="item-title">1.1 您主动提供的信息</text>
						<text class="item-text">当您注册账号、使用我们的服务或联系客服时，您可能会提供以下信息：</text>
						<view class="item-list">
							<text class="list-item">• 个人识别信息：如姓名、电话号码、电子邮件地址等</text>
							<text class="list-item">• 账号信息：如登录凭证、密码等</text>
							<text class="list-item">• 儿童信息：如儿童年龄、学习进度、兴趣爱好等（需要家长授权）</text>
							<text class="list-item">• 您通过客服或参与问卷调查时提供的其他信息</text>
						</view>
					</view>
					
					<view class="content-item">
						<text class="item-title">1.2 我们自动收集的信息</text>
						<text class="item-text">当您使用我们的产品或服务时，我们可能会自动收集以下信息：</text>
						<view class="item-list">
							<text class="list-item">• 设备信息：如设备型号、操作系统版本、设备设置、应用程序版本等</text>
							<text class="list-item">• 日志信息：如IP地址、搜索查询、访问日期和时间、使用的功能等</text>
							<text class="list-item">• 位置信息：如您的大致位置（基于IP地址）或精确位置（如您授权）</text>
							<text class="list-item">• 使用数据：如您与应用的交互方式、访问的页面、学习时长等</text>
						</view>
					</view>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">二、我们如何使用您的信息</view>
				<view class="section-content">
					<view class="item-list">
						<text class="list-item">• 提供、维护和改进我们的产品和服务</text>
						<text class="list-item">• 处理您的注册和管理您的账户</text>
						<text class="list-item">• 响应您的评论、问题和请求</text>
						<text class="list-item">• 向您推送个性化的学习内容和推荐</text>
						<text class="list-item">• 分析用户行为，改进用户体验</text>
						<text class="list-item">• 保障账号和系统安全</text>
						<text class="list-item">• 履行法律法规规定的义务</text>
					</view>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">三、儿童信息保护</view>
				<view class="section-content">
					<text class="item-text">我们高度重视儿童个人信息的保护：</text>
					<view class="item-list">
						<text class="list-item">• 我们需要首先获得家长/监护人的明确同意，才会收集、使用儿童的个人信息</text>
						<text class="list-item">• 家长/监护人有权审核其儿童的个人信息、要求删除或拒绝我们进一步收集或使用儿童信息</text>
						<text class="list-item">• 我们仅收集必要的儿童信息，用于提供教育服务和改善学习体验</text>
						<text class="list-item">• 我们采取特殊措施保障儿童信息安全，并定期评估关于儿童信息收集的必要性</text>
					</view>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">四、信息共享与转让</view>
				<view class="section-content">
					<text class="item-text">我们不会将您的个人信息出售给第三方。在以下情况下，我们可能会共享您的个人信息：</text>
					<view class="item-list">
						<text class="list-item">• 获得您的明确同意后</text>
						<text class="list-item">• 与我们的关联公司共享，用于本隐私政策中描述的目的</text>
						<text class="list-item">• 与提供服务的供应商和合作伙伴共享，如云服务提供商、数据分析服务商等</text>
						<text class="list-item">• 应法律法规要求、法律程序、诉讼和/或公共和政府机构的要求</text>
					</view>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">五、数据安全</view>
				<view class="section-content">
					<text class="item-text">我们致力于保护您的个人信息安全：</text>
					<view class="item-list">
						<text class="list-item">• 采用加密技术存储和传输数据</text>
						<text class="list-item">• 实施访问控制措施，确保只有授权人员才能访问个人信息</text>
						<text class="list-item">• 定期审查信息收集、存储和处理实践，防止未经授权的访问</text>
					</view>
					<text class="item-text item-note">尽管我们采取了上述安全措施，但请注意，互联网和移动平台本质上无法保证100%的安全性。</text>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">六、您的权利</view>
				<view class="section-content">
					<text class="item-text">根据适用的法律，您可能享有以下权利：</text>
					<view class="item-list">
						<text class="list-item">• 访问权：您有权访问我们持有的关于您的个人信息</text>
						<text class="list-item">• 更正权：您有权要求更正不准确的个人信息</text>
						<text class="list-item">• 删除权：在特定情况下，您有权要求删除您的个人信息</text>
						<text class="list-item">• 限制处理权：在特定情况下，您有权限制我们处理您的个人信息</text>
						<text class="list-item">• 反对权：在特定情况下，您有权反对我们处理您的个人信息</text>
						<text class="list-item">• 数据可携带权：您有权以结构化、常用和机器可读的格式接收您的个人信息</text>
					</view>
					<text class="item-text">如需行使上述权利，请通过以下联系方式与我们联系。</text>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">七、Cookie和类似技术</view>
				<view class="section-content">
					<text class="item-text">我们使用Cookie和类似技术收集和存储信息，以提供更好的用户体验，并分析我们服务的使用情况。</text>
					<text class="item-text">您可以通过浏览器设置拒绝Cookie，但这可能会影响我们服务的某些功能。</text>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">八、隐私政策的变更</view>
				<view class="section-content">
					<text class="item-text">我们可能会不时更新本隐私政策。当我们进行重大变更时，我们会在应用内通知您，并更新页面上的"更新日期"。</text>
					<text class="item-text">建议您定期查看本隐私政策，以了解我们如何保护您的信息。</text>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">九、联系我们</view>
				<view class="section-content">
					<text class="item-text">如果您对本隐私政策有任何疑问、意见或请求，请通过以下方式联系我们：</text>
					<view class="contact-info">
						<text class="contact-item">电子邮件：privacy@xiaozhi.com</text>
						<text class="contact-item">电话：400-123-4567</text>
						<text class="contact-item">地址：北京市海淀区中关村软件园</text>
					</view>
					<text class="item-text">我们会在收到您的请求后30天内回复。</text>
				</view>
			</view>
			
			<view class="end-statement">
				<text>感谢您阅读我们的隐私政策。使用我们的服务，即表示您理解并同意本隐私政策。</text>
			</view>
		</scroll-view>
	</view>
</template>

<script>
export default {
	data() {
		return {}
	},
	methods: {
		navigateBack() {
			uni.navigateBack()
		},
		printPolicy() {
			uni.showToast({
				title: '正在准备打印...',
				icon: 'none'
			})
			// In a real app, implement print functionality
		}
	}
}
</script>

<style>
.privacy-container {
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

.privacy-content {
	flex: 1;
	padding: 30rpx;
}

.last-updated {
	font-size: 26rpx;
	color: #6c757d;
	margin-bottom: 40rpx;
	text-align: right;
}

.privacy-intro {
	margin-bottom: 40rpx;
}

.intro-text {
	font-size: 30rpx;
	color: #212529;
	line-height: 1.6;
}

.section-container {
	margin-bottom: 40rpx;
}

.section-title {
	font-size: 34rpx;
	font-weight: 600;
	color: #212529;
	margin-bottom: 20rpx;
	padding-bottom: 10rpx;
	border-bottom: 1rpx solid #f5f5f5;
}

.section-content {
	padding: 0 20rpx;
}

.content-item {
	margin-bottom: 30rpx;
}

.item-title {
	font-size: 30rpx;
	font-weight: 500;
	color: #212529;
	margin-bottom: 10rpx;
	display: block;
}

.item-text {
	font-size: 28rpx;
	color: #212529;
	line-height: 1.6;
	display: block;
	margin-bottom: 10rpx;
}

.item-note {
	color: #6c757d;
	font-style: italic;
	margin-top: 20rpx;
}

.item-list {
	margin: 10rpx 0 20rpx 0;
}

.list-item {
	font-size: 28rpx;
	color: #212529;
	line-height: 1.6;
	display: block;
	margin-bottom: 10rpx;
	padding-left: 10rpx;
}

.contact-info {
	background-color: #f8f9fa;
	border-radius: 10rpx;
	padding: 20rpx;
	margin: 20rpx 0;
}

.contact-item {
	font-size: 28rpx;
	color: #212529;
	line-height: 1.6;
	display: block;
	margin-bottom: 10rpx;
}

.end-statement {
	text-align: center;
	margin: 60rpx 0;
	font-size: 28rpx;
	color: #6c757d;
	padding: 0 40rpx;
	line-height: 1.6;
}
</style> 