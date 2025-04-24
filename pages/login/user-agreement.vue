<template>
  <view class="agreement-container">
    <!-- Header -->
    <view class="agreement-header">
      <view class="back-button" @tap="navigateBack">
        <text class="back-icon">←</text>
      </view>
      <text class="page-title">用户协议</text>
      <view class="placeholder"></view>
    </view>

    <!-- Agreement Content -->
    <scroll-view class="agreement-content" scroll-y>
      <view class="agreement-section">
        <text class="agreement-title">小智App用户协议</text>
        <text class="agreement-date">更新日期：2023年12月1日</text>
        
        <view class="agreement-paragraph">
          <text>欢迎使用小智App（"本应用"）。本应用由XXXX科技有限公司（"我们"）提供。在您使用本应用之前，请仔细阅读并理解本用户协议（"本协议"）。本协议构成您与我们之间关于使用本应用服务的法律协议。</text>
        </view>
        
        <view class="agreement-paragraph">
          <text>通过注册、登录、使用本应用，您确认已经阅读、理解并同意接受本协议的所有条款和条件，包括我们不时发布的对本协议的修改。如果您不同意本协议的任何条款，请停止注册或使用本应用。</text>
        </view>
        
        <view class="section-item">
          <text class="section-title">1. 服务内容</text>
          <view class="section-content">
            <text>1.1 本应用是一款面向儿童的AI伴学应用，提供学习内容、互动对话、成长记录等服务。</text>
            <text>1.2 我们保留随时修改、中断或终止部分或全部服务的权利，对此我们不需要对您或任何第三方负责。</text>
            <text>1.3 您理解并同意，我们提供的服务是按照现有技术和条件所能达到的现状提供的。我们会尽最大努力保证服务的连贯性和安全性，但不能保证服务不会中断，也不能保证服务的及时性、安全性和准确性。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">2. 账户注册与使用</text>
          <view class="section-content">
            <text>2.1 您在注册账户时需要提供真实、准确、完整的个人资料，并在资料变更时及时更新。</text>
            <text>2.2 您需要妥善保管账户和密码，并对通过您的账户和密码进行的所有活动和事件负责。</text>
            <text>2.3 如发现任何未经授权使用您账户的情况，请立即通知我们。</text>
            <text>2.4 我们有权根据本协议中的条款，暂停或终止向您提供服务，且无需为此向您承担任何责任。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">3. 用户行为规范</text>
          <view class="section-content">
            <text>3.1 您使用本应用服务时必须遵守中华人民共和国相关法律法规以及本协议的规定。</text>
            <text>3.2 您不得利用本应用从事以下行为：</text>
            <text class="indent">a) 发布、传送、传播、储存违反国家法律法规禁止的内容；</text>
            <text class="indent">b) 发布、传送、传播、储存侵害他人名誉权、肖像权、知识产权、商业秘密等合法权利的内容；</text>
            <text class="indent">c) 使用本应用服务进行任何可能对互联网正常运转造成不利影响的行为；</text>
            <text class="indent">d) 利用本应用服务进行任何不利于未成年人身心健康的行为；</text>
            <text class="indent">e) 其他我们认为不适当的行为。</text>
            <text>3.3 若您违反上述规定，我们有权立即终止向您提供服务，并采取包括但不限于删除内容、暂停或注销账户等措施。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">4. 知识产权</text>
          <view class="section-content">
            <text>4.1 本应用及其相关服务中的所有内容，包括但不限于文字、图片、音频、视频、软件、程序、代码等均受知识产权法律法规保护。</text>
            <text>4.2 未经我们或相关权利人事先书面许可，您不得以任何方式擅自使用、复制、分发、传播或公开展示上述内容。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">5. 隐私保护</text>
          <view class="section-content">
            <text>5.1 我们重视您的隐私保护，并根据《隐私政策》收集、使用、存储和保护您的个人信息。</text>
            <text>5.2 您同意，我们可以根据《隐私政策》的规定收集、使用、存储和保护您的个人信息。</text>
            <text>5.3 请您查阅我们的《隐私政策》以了解更多关于我们如何处理您个人信息的详情。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">6. 免责声明</text>
          <view class="section-content">
            <text>6.1 您理解并同意，在使用本应用服务过程中可能遇到的风险将完全由您自己承担。我们对服务不作任何类型的担保。</text>
            <text>6.2 您理解并同意，我们不对因下述任一情况而导致的任何损害赔偿承担责任：</text>
            <text class="indent">a) 使用或无法使用本应用服务；</text>
            <text class="indent">b) 通过本应用购买或获取的任何商品、资料、信息或服务；</text>
            <text class="indent">c) 您的传输或数据遭到未获授权的访问或变更；</text>
            <text class="indent">d) 本应用中任何第三方的行为或内容；</text>
            <text class="indent">e) 其他与本应用相关的事宜。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">7. 协议修改</text>
          <view class="section-content">
            <text>7.1 我们保留随时修改本协议的权利，修改后的协议将在本应用上公布。</text>
            <text>7.2 如果您不同意修改后的协议，您可以选择停止使用本应用。如果您继续使用本应用，则视为您接受修改后的协议。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">8. 适用法律与争议解决</text>
          <view class="section-content">
            <text>8.1 本协议适用中华人民共和国法律。</text>
            <text>8.2 因本协议引起的或与本协议有关的任何争议，应通过友好协商解决；协商不成的，任何一方均可将争议提交至本公司所在地有管辖权的人民法院。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">9. 联系我们</text>
          <view class="section-content">
            <text>如果您对本协议或本应用服务有任何问题、意见或建议，请通过以下方式与我们联系：</text>
            <text>电子邮件：support@xiaozhi.com</text>
            <text>客服电话：400-123-4567</text>
          </view>
        </view>
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
      uni.navigateBack();
    }
  }
}
</script>

<style>
.agreement-container {
  min-height: 100vh;
  background-color: #FFFFFF;
  display: flex;
  flex-direction: column;
}

/* Header */
.agreement-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 30rpx;
  position: relative;
  border-bottom: 1rpx solid #EAEAEA;
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

/* Agreement Content */
.agreement-content {
  flex: 1;
  padding: 30rpx;
}

.agreement-section {
  margin-bottom: 40rpx;
}

.agreement-title {
  font-size: 40rpx;
  font-weight: bold;
  color: #333333;
  display: block;
  text-align: center;
  margin-bottom: 20rpx;
}

.agreement-date {
  font-size: 26rpx;
  color: #999999;
  display: block;
  text-align: center;
  margin-bottom: 40rpx;
}

.agreement-paragraph {
  margin-bottom: 30rpx;
}

.agreement-paragraph text {
  font-size: 30rpx;
  color: #333333;
  line-height: 1.6;
}

.section-item {
  margin-bottom: 40rpx;
}

.section-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333333;
  margin-bottom: 20rpx;
  display: block;
}

.section-content {
  display: flex;
  flex-direction: column;
}

.section-content text {
  font-size: 30rpx;
  color: #333333;
  line-height: 1.6;
  margin-bottom: 15rpx;
}

.indent {
  padding-left: 40rpx;
}
</style> 