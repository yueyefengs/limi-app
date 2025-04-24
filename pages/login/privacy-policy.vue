<template>
  <view class="privacy-container">
    <!-- Header -->
    <view class="privacy-header">
      <view class="back-button" @tap="navigateBack">
        <text class="back-icon">←</text>
      </view>
      <text class="page-title">隐私政策</text>
      <view class="placeholder"></view>
    </view>

    <!-- Privacy Content -->
    <scroll-view class="privacy-content" scroll-y>
      <view class="privacy-section">
        <text class="privacy-title">小智App隐私政策</text>
        <text class="privacy-date">更新日期：2023年12月1日</text>
        
        <view class="privacy-paragraph">
          <text>小智App（"我们"）非常重视用户的隐私和个人信息保护。本隐私政策旨在向您说明我们如何收集、使用、存储和共享您的个人信息，以及您享有的相关权利。请您在使用我们的服务前仔细阅读本隐私政策。</text>
        </view>
        
        <view class="privacy-paragraph">
          <text>特别提示：小智App是一款面向儿童的AI伴学应用。我们深知儿童个人信息的重要性，并严格遵守相关法律法规对儿童个人信息的保护要求。如您是14周岁以下的未成年人，请在您的父母或其他监护人的陪同下阅读本政策，并在征得您父母或其他监护人同意的前提下使用我们的服务。</text>
        </view>
        
        <view class="section-item">
          <text class="section-title">1. 我们收集的信息</text>
          <view class="section-content">
            <text>1.1 您提供的信息：</text>
            <text class="indent">a) 账户信息：当您注册账户时，我们会收集您的手机号码、电子邮箱地址、密码等信息。</text>
            <text class="indent">b) 个人资料：如您选择提供，我们会收集孩子的姓名、年龄、性别、头像等信息。</text>
            <text class="indent">c) 家长联系信息：作为儿童监护人，我们会收集您的联系方式，以便必要时与您取得联系。</text>
            <text>1.2 使用服务时收集的信息：</text>
            <text class="indent">a) 学习记录：包括孩子的学习进度、完成的内容、测试结果等。</text>
            <text class="indent">b) 互动内容：孩子与AI助手的对话内容、问题咨询等。</text>
            <text class="indent">c) 设备信息：设备型号、操作系统版本、浏览器类型、语言设置等。</text>
            <text class="indent">d) 日志信息：使用时间、使用时长、崩溃报告等。</text>
            <text>1.3 位置信息：我们仅在必要且征得您明确同意的情况下收集位置信息。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">2. 我们如何使用收集的信息</text>
          <view class="section-content">
            <text>2.1 提供、维护和改进我们的服务：</text>
            <text class="indent">a) 创建和管理您的账户；</text>
            <text class="indent">b) 提供个性化的学习内容和建议；</text>
            <text class="indent">c) 分析使用情况，优化产品设计和体验。</text>
            <text>2.2 安全与保障：</text>
            <text class="indent">a) 验证用户身份，保护账户安全；</text>
            <text class="indent">b) 监测和防止欺诈行为和安全风险；</text>
            <text class="indent">c) 排查产品故障，确保服务稳定运行。</text>
            <text>2.3 通信与客户服务：</text>
            <text class="indent">a) 回应您的问询和反馈；</text>
            <text class="indent">b) 发送服务通知和更新信息；</text>
            <text class="indent">c) 经您同意后，发送营销信息。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">3. 儿童个人信息的特殊保护</text>
          <view class="section-content">
            <text>3.1 儿童信息收集原则：我们严格遵循"必要性"原则收集儿童个人信息，仅收集为实现产品功能所必需的信息。</text>
            <text>3.2 监护人同意：对于14周岁以下儿童，我们会先行获取其监护人的明确同意后，再收集和使用儿童个人信息。</text>
            <text>3.3 内容审核：我们采取严格的内容审核机制，确保向儿童提供的内容安全、健康、适龄。</text>
            <text>3.4 限制使用：我们不会将收集的儿童个人信息用于与产品或服务功能无关的目的，不会向儿童推送商业广告。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">4. 信息共享与披露</text>
          <view class="section-content">
            <text>4.1 我们不会出售、出租、转让或以其他方式与任何第三方共享您的个人信息，但以下情况除外：</text>
            <text class="indent">a) 获得您的明确同意；</text>
            <text class="indent">b) 与我们的关联公司共享，用于提供服务和内部管理；</text>
            <text class="indent">c) 与授权合作伙伴共享，以提供特定服务（如技术支持、数据分析）；</text>
            <text class="indent">d) 基于法律法规、法律程序、诉讼和/或政府主管部门的要求。</text>
            <text>4.2 我们会与所有第三方签署严格的保密协议，确保他们按照我们的隐私政策和相关法律法规处理您的个人信息。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">5. 信息安全</text>
          <view class="section-content">
            <text>5.1 我们采取业界标准的安全措施保护您的个人信息，防止未经授权的访问、公开披露、使用、修改、损坏或丢失。</text>
            <text>5.2 我们的安全措施包括但不限于：</text>
            <text class="indent">a) 数据加密传输和存储；</text>
            <text class="indent">b) 严格的访问控制和监控机制；</text>
            <text class="indent">c) 定期安全评估和漏洞修复；</text>
            <text class="indent">d) 员工安全意识培训。</text>
            <text>5.3 尽管我们竭尽全力保护您的信息安全，但请理解互联网环境并非百分之百安全，我们无法保证信息传输的绝对安全。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">6. 您的权利</text>
          <view class="section-content">
            <text>6.1 访问与更正：您有权访问、更正您的个人信息。</text>
            <text>6.2 删除：在特定情况下，您有权要求删除您的个人信息。</text>
            <text>6.3 撤回同意：您有权随时撤回您此前作出的同意。</text>
            <text>6.4 注销账户：您可以通过应用中的设置注销您的账户。注销后，我们将停止提供服务，并依据您的要求删除您的个人信息。</text>
            <text>6.5 投诉建议：如您对我们的隐私政策或个人信息处理有任何疑问、意见或建议，可通过本政策末尾提供的联系方式与我们联系。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">7. Cookie和类似技术</text>
          <view class="section-content">
            <text>7.1 我们使用Cookie和类似技术来提供、保护和改进我们的产品和服务，例如记住您的偏好设置、提供个性化体验等。</text>
            <text>7.2 您可以通过浏览器设置管理Cookie。但请注意，禁用Cookie可能会影响您使用我们服务的部分功能。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">8. 政策更新</text>
          <view class="section-content">
            <text>8.1 我们可能会不时更新本隐私政策，更新后的政策将在应用内发布。</text>
            <text>8.2 对于重大变更，我们会通过应用内通知或其他方式提醒您。</text>
            <text>8.3 如您继续使用我们的服务，即表示您同意接受修改后的隐私政策。</text>
          </view>
        </view>
        
        <view class="section-item">
          <text class="section-title">9. 联系我们</text>
          <view class="section-content">
            <text>如果您对本隐私政策有任何疑问、意见或建议，请通过以下方式与我们联系：</text>
            <text>电子邮件：privacy@xiaozhi.com</text>
            <text>隐私专线：400-123-4567</text>
            <text>我们会在收到您的请求后30天内回复。</text>
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
.privacy-container {
  min-height: 100vh;
  background-color: #FFFFFF;
  display: flex;
  flex-direction: column;
}

/* Header */
.privacy-header {
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

/* Privacy Content */
.privacy-content {
  flex: 1;
  padding: 30rpx;
}

.privacy-section {
  margin-bottom: 40rpx;
}

.privacy-title {
  font-size: 40rpx;
  font-weight: bold;
  color: #333333;
  display: block;
  text-align: center;
  margin-bottom: 20rpx;
}

.privacy-date {
  font-size: 26rpx;
  color: #999999;
  display: block;
  text-align: center;
  margin-bottom: 40rpx;
}

.privacy-paragraph {
  margin-bottom: 30rpx;
}

.privacy-paragraph text {
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