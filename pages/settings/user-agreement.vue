<template>
	<view class="agreement-container">
		<!-- Header -->
		<view class="page-header">
			<view class="back-button" @click="navigateBack">
				<text class="iconfont icon-left"></text>
			</view>
			<text class="page-title">用户协议</text>
			<view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
		</view>
		
		<scroll-view scroll-y class="agreement-content">
			<view class="last-updated">更新日期：2023年12月1日</view>
			
			<view class="agreement-intro">
				<text class="intro-text">欢迎使用小智App！在使用我们的服务前，请您仔细阅读本用户协议（"协议"）。本协议是您与小智App（以下简称"我们"或"小智"）之间关于使用小智服务所订立的协议。本协议对您和小智均具有法律约束力。</text>
			</view>
			
			<view class="section-container">
				<view class="section-title">一、接受条款</view>
				<view class="section-content">
					<text class="item-text">通过下载、安装、注册或使用小智App，您确认已阅读、理解并同意受本协议的约束。如您不同意本协议的任何内容，请勿使用我们的服务。如您未满18周岁，请在监护人的陪同下阅读本协议，并在获得监护人同意后使用我们的服务。</text>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">二、服务内容及变更</view>
				<view class="section-content">
					<text class="item-text">2.1 小智App主要为5-12岁儿童提供智能伴学服务，包括但不限于：学习内容推荐、AI互动对话、智能学习管理等。</text>
					<text class="item-text">2.2 我们保留随时修改、暂停或终止部分或全部服务的权利。服务的重大变更会通过应用内通知、电子邮件或网站公告等方式告知用户。</text>
					<text class="item-text">2.3 您理解并同意，我们提供的服务是按照现有技术和条件所能达到的现状提供的。我们会尽最大努力确保服务的连贯性和安全性，但不能保证服务不会中断，也不能保证服务中的所有缺陷都能得到及时修复。</text>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">三、账号注册与安全</view>
				<view class="section-content">
					<text class="item-text">3.1 您需要注册账号才能使用我们的完整服务。在注册过程中，您需要提供手机号码或电子邮箱等信息，并创建密码。</text>
					<text class="item-text">3.2 您应当提供真实、准确、完整的资料，并在资料发生变更时及时更新。如您提供的资料不真实、不准确或不完整，我们有权暂停或终止您的账号。</text>
					<text class="item-text">3.3 您的账号和密码由您自行保管，您应当对账号下的所有行为负责。如发现有他人未经授权使用您的账号，您应立即通知我们。</text>
					<text class="item-text">3.4 为儿童创建账号的监护人应确保对儿童使用我们服务的情况进行妥善监督。</text>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">四、用户行为规范</view>
				<view class="section-content">
					<text class="item-text">您在使用我们的服务时，必须遵守中华人民共和国相关法律法规及本协议的规定。您同意不会利用我们的服务从事以下行为：</text>
					<view class="item-list">
						<text class="list-item">• 发布、传输、传播违反国家法律法规的信息</text>
						<text class="list-item">• 侵犯他人知识产权、商业秘密、隐私权或其他合法权益</text>
						<text class="list-item">• 干扰我们服务的正常运行，或侵入我们的服务器和网络</text>
						<text class="list-item">• 传播广告信息或从事其他商业活动</text>
						<text class="list-item">• 冒充他人或在不授权的情况下访问他人账号</text>
						<text class="list-item">• 其他违反法律法规、社会公德或可能损害我们或其他用户权益的行为</text>
					</view>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">五、知识产权</view>
				<view class="section-content">
					<text class="item-text">5.1 小智App及其服务中的所有内容，包括但不限于文字、图片、音频、视频、软件、程序、代码等，均由我们或第三方版权所有者拥有知识产权。未经我们或第三方版权所有者书面许可，您不得以任何形式复制、修改、传播或使用上述内容。</text>
					<text class="item-text">5.2 我们尊重知识产权，并希望用户也尊重知识产权。如您认为我们服务中的内容侵犯了您的知识产权，请按照联系方式联系我们，我们将依法处理。</text>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">六、付费服务</view>
				<view class="section-content">
					<text class="item-text">6.1 我们的部分服务可能需要付费才能使用。在您使用付费服务前，我们会明确告知您服务的内容、价格、付费方式等信息。</text>
					<text class="item-text">6.2 您可以通过我们提供的支付渠道进行付费。付费成功后，您可以在规定时间内使用相应的付费服务。</text>
					<text class="item-text">6.3 对于已购买的付费服务，除法律规定或双方另有约定外，一般不支持退款。特殊情况下的退款处理将根据具体情况评估。</text>
					<text class="item-text">6.4 儿童用户在进行任何付费操作前，必须获得其监护人的同意。</text>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">七、免责声明</view>
				<view class="section-content">
					<text class="item-text">7.1 您理解并同意，我们无法保证服务一定能满足您的所有需求，也不保证服务不会中断，不保证服务的及时性、安全性、准确性。</text>
					<text class="item-text">7.2 对于因网络状况、通信线路、第三方网站或管理部门的要求等任何原因而导致的服务中断或不能满足用户要求的情况，我们不承担任何责任。</text>
					<text class="item-text">7.3 我们的服务可能包含链接至第三方网站的链接，该等链接并不表示我们对第三方网站内容的认可。我们对该等第三方网站的内容、隐私政策及做法不负任何责任。</text>
					<text class="item-text">7.4 我们提供的教育内容仅供参考，不能替代专业的教育指导。家长和监护人应根据儿童的实际情况做出判断。</text>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">八、协议的变更</view>
				<view class="section-content">
					<text class="item-text">8.1 我们保留在必要时修改本协议的权利。协议修改后，如您继续使用我们的服务，即视为您已接受修改后的协议。</text>
					<text class="item-text">8.2 当我们对本协议进行重大变更时，我们会通过应用内通知、电子邮件或网站公告等方式通知您。</text>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">九、终止</view>
				<view class="section-content">
					<text class="item-text">9.1 如您违反本协议的任何条款，我们有权暂停或终止您对服务的使用权，且无需向您承担任何责任。</text>
					<text class="item-text">9.2 您可以通过注销账号的方式终止本协议。注销账号后，您将无法使用该账号及相关服务，且我们将依据隐私政策处理您的个人信息。</text>
					<text class="item-text">9.3 终止协议后，与本协议中约定的争议解决方式、法律适用等条款仍然有效。</text>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">十、法律适用与争议解决</view>
				<view class="section-content">
					<text class="item-text">10.1 本协议的订立、执行和解释及争议的解决均应适用中华人民共和国法律。</text>
					<text class="item-text">10.2 因本协议引起的或与本协议有关的任何争议，双方应友好协商解决；协商不成的，任何一方均有权将争议提交至北京市海淀区人民法院诉讼解决。</text>
				</view>
			</view>
			
			<view class="section-container">
				<view class="section-title">十一、联系我们</view>
				<view class="section-content">
					<text class="item-text">如您对本协议有任何疑问，或在使用我们服务的过程中有任何问题，可通过以下方式联系我们：</text>
					<view class="contact-info">
						<text class="contact-item">电子邮件：support@xiaozhi.com</text>
						<text class="contact-item">电话：400-123-4567</text>
						<text class="contact-item">地址：北京市海淀区中关村软件园</text>
					</view>
				</view>
			</view>
			
			<view class="end-statement">
				<text>本协议最终解释权归小智App所有。</text>
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
		}
	}
}
</script>

<style>
.agreement-container {
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

.agreement-content {
	flex: 1;
	padding: 30rpx;
}

.last-updated {
	font-size: 26rpx;
	color: #6c757d;
	margin-bottom: 40rpx;
	text-align: right;
}

.agreement-intro {
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

.item-text {
	font-size: 28rpx;
	color: #212529;
	line-height: 1.6;
	display: block;
	margin-bottom: 20rpx;
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