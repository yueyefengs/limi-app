<template>
  <view class="chat-history-container">
    <!-- 状态栏 -->
    <view class="status-bar">
      <text class="time">{{ currentTime }}</text>
      <view class="icons">
        <text class="fa fa-signal"></text>
        <text class="fa fa-wifi"></text>
        <text class="fa fa-battery-full"></text>
      </view>
    </view>
    
    <!-- 页面头部 -->
    <view class="page-header">
      <view class="back-button" @click="navigateBack">
        <text class="fa fa-chevron-left"></text>
      </view>
      <text class="page-title">聊天记录</text>
      <view class="filter-button" @click="showFilter">
        <text class="fa fa-filter"></text>
      </view>
    </view>
    
    <!-- 标签页 -->
    <view class="tabs">
      <view 
        v-for="(tab, index) in tabs" 
        :key="index" 
        class="tab" 
        :class="{ active: currentTab === index }"
        @click="switchTab(index)"
      >
        <text>{{ tab }}</text>
      </view>
    </view>
    
    <!-- 搜索栏 -->
    <view class="search-bar">
      <text class="fa fa-search"></text>
      <input type="text" class="search-input" placeholder="搜索聊天记录" v-model="searchText" />
    </view>
    
    <!-- 主要内容区域 -->
    <scroll-view class="content" scroll-y="true" v-if="filteredRecords.length > 0">
      <!-- 日期分组 -->
      <block v-for="(group, date) in groupedRecords" :key="date">
        <view class="date-divider">{{ formatGroupDate(date) }}</view>
        
        <!-- 聊天列表项 -->
        <view 
          class="chat-session" 
          v-for="record in group" 
          :key="record.id"
          @click="viewChatDetail(record.id)"
        >
          <view class="session-header">
            <view class="session-icon">
              <text :class="getSessionIcon(record.type)"></text>
            </view>
            <view class="session-info">
              <text class="session-title">{{ record.title }}</text>
              <text class="session-time">{{ formatTime(record.date) }}</text>
            </view>
          </view>
          <text class="session-preview">{{ record.lastMessage }}</text>
          
          <!-- 操作按钮 -->
          <view class="session-actions">
            <view class="action-button" @click.stop="shareChatRecord(record.id)">
              <text class="fa fa-share-alt"></text>
            </view>
            <view class="action-button" @click.stop="deleteChatRecord(record.id)">
              <text class="fa fa-trash"></text>
            </view>
          </view>
        </view>
      </block>
    </scroll-view>
    
    <!-- 空状态 -->
    <view v-else class="empty-state">
      <image class="empty-icon" src="/static/icons/empty-chat.png" mode="aspectFit"></image>
      <text class="empty-text">暂无聊天记录</text>
      <button class="start-chat-btn" @click="navigateToChat">开始聊天</button>
    </view>
    
    <!-- 删除确认弹窗 -->
    <view v-if="showDeleteModal" class="modal-overlay" @click="cancelDelete">
      <view class="modal-content" @click.stop>
        <view class="modal-title">删除聊天记录</view>
        <view class="modal-text">
          {{ deleteAllMode ? '确定要删除所有聊天记录吗？' : '确定要删除这条聊天记录吗？' }}
        </view>
        <view class="modal-actions">
          <button class="modal-btn cancel-btn" @click="cancelDelete">取消</button>
          <button class="modal-btn confirm-btn" @click="confirmDelete">确认删除</button>
        </view>
      </view>
    </view>
    
    <!-- 底部导航栏 -->
    <view class="navbar">
      <view class="nav-item" @click="navigateTo('/pages/home/index')">
        <text class="fa fa-home"></text>
        <text>首页</text>
      </view>
      <view class="nav-item active">
        <text class="fa fa-comment"></text>
        <text>聊天</text>
      </view>
      <view class="nav-item" @click="navigateTo('/pages/content/index')">
        <text class="fa fa-compass"></text>
        <text>发现</text>
      </view>
      <view class="nav-item" @click="navigateTo('/pages/profile/index')">
        <text class="fa fa-user"></text>
        <text>我的</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      chatRecords: [],
      showDeleteModal: false,
      deleteAllMode: false,
      currentDeleteId: null,
      currentTime: '9:41',
      tabs: ['全部记录', '问答类', '故事类', '游戏类'],
      currentTab: 0,
      searchText: ''
    }
  },
  computed: {
    filteredRecords() {
      if (!this.chatRecords.length) return [];
      
      let records = [...this.chatRecords];
      
      // 标签过滤
      if (this.currentTab !== 0) {
        const types = ['', 'qa', 'story', 'game'];
        records = records.filter(r => r.type === types[this.currentTab]);
      }
      
      // 搜索过滤
      if (this.searchText) {
        const searchLower = this.searchText.toLowerCase();
        records = records.filter(r => 
          r.title.toLowerCase().includes(searchLower) ||
          r.lastMessage.toLowerCase().includes(searchLower)
        );
      }
      
      return records;
    },
    groupedRecords() {
      const groups = {};
      
      this.filteredRecords.forEach(record => {
        const date = this.formatDate(record.date, 'grouping');
        if (!groups[date]) {
          groups[date] = [];
        }
        groups[date].push(record);
      });
      
      return groups;
    }
  },
  onLoad() {
    this.loadChatRecords();
    this.updateCurrentTime();
    
    // 每分钟更新一次时间
    setInterval(this.updateCurrentTime, 60000);
  },
  methods: {
    updateCurrentTime() {
      const now = new Date();
      const hours = now.getHours().toString().padStart(2, '0');
      const minutes = now.getMinutes().toString().padStart(2, '0');
      this.currentTime = `${hours}:${minutes}`;
    },
    navigateBack() {
      uni.navigateBack();
    },
    navigateToChat() {
      uni.navigateTo({
        url: '/pages/chat/index'
      });
    },
    navigateTo(url) {
      uni.switchTab({
        url: url
      });
    },
    loadChatRecords() {
      // 在真实应用中从存储或API加载
      // 这是示例数据
      this.chatRecords = [
        {
          id: '1',
          date: new Date(new Date().getTime()),
          title: '恐龙知识问答',
          lastMessage: '小明: 霸王龙有多大？ 小智: 霸王龙是史前最大的陆地掠食动物之一，身长可达12米，站立高度约5米，体重可达8吨。它们生活在白垩纪晚期...',
          messageCount: 12,
          type: 'qa'
        },
        {
          id: '2',
          date: new Date(new Date().getTime()),
          title: '睡前故事时间',
          lastMessage: '小明: 给我讲一个关于小兔子的故事 小智: 从前，有一只名叫跳跳的小兔子，它生活在一片美丽的森林里...',
          messageCount: 8,
          type: 'story'
        },
        {
          id: '3',
          date: new Date(new Date().getTime() - 24 * 60 * 60 * 1000),
          title: '儿歌点播',
          lastMessage: '小明: 我想听小星星 小智: 好的，我来为你播放《小星星》 一闪一闪亮晶晶，满天都是小星星...',
          messageCount: 15,
          type: 'story'
        },
        {
          id: '4',
          date: new Date(new Date().getTime() - 24 * 60 * 60 * 1000),
          title: '猜谜语游戏',
          lastMessage: '小智: 我来出一个谜语，什么东西有头无脚？ 小明: 是钉子吗？ 小智: 不对，再猜猜看！ 小明: 是床吗？...',
          messageCount: 10,
          type: 'game'
        },
        {
          id: '5',
          date: new Date(new Date().getTime() - 2 * 24 * 60 * 60 * 1000),
          title: '数学练习',
          lastMessage: '小明: 5+7等于多少？ 小智: 5+7=12 小明: 那8+6呢？ 小智: 8+6=14 小明: 谢谢小智！...',
          messageCount: 7,
          type: 'qa'
        }
      ];
    },
    formatDate(date, type = 'display') {
      const now = new Date();
      const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
      const yesterday = new Date(today);
      yesterday.setDate(yesterday.getDate() - 1);
      
      if (type === 'grouping') {
        // 用于分组的日期格式
        if (date.getDate() === now.getDate() && 
            date.getMonth() === now.getMonth() && 
            date.getFullYear() === now.getFullYear()) {
          return 'today';
        } else if (date.getDate() === yesterday.getDate() && 
                  date.getMonth() === yesterday.getMonth() && 
                  date.getFullYear() === yesterday.getFullYear()) {
          return 'yesterday';
        } else {
          return `${date.getFullYear()}-${(date.getMonth()+1).toString().padStart(2, '0')}-${date.getDate().toString().padStart(2, '0')}`;
        }
      } else {
        // 用于显示的日期格式
        if (date.getDate() === now.getDate() && 
            date.getMonth() === now.getMonth() && 
            date.getFullYear() === now.getFullYear()) {
          return '今天';
        } else if (date.getDate() === yesterday.getDate() && 
                  date.getMonth() === yesterday.getMonth() && 
                  date.getFullYear() === yesterday.getFullYear()) {
          return '昨天';
        } else {
          return `${date.getFullYear()}-${(date.getMonth()+1).toString().padStart(2, '0')}-${date.getDate().toString().padStart(2, '0')}`;
        }
      }
    },
    formatGroupDate(groupKey) {
      if (groupKey === 'today') return '今天';
      if (groupKey === 'yesterday') return '昨天';
      return groupKey;
    },
    formatTime(date) {
      return `${date.getHours().toString().padStart(2, '0')}:${date.getMinutes().toString().padStart(2, '0')}`;
    },
    getSessionIcon(type) {
      switch(type) {
        case 'qa': return 'fa fa-robot';
        case 'story': return 'fa fa-book';
        case 'game': return 'fa fa-gamepad';
        default: return 'fa fa-comment';
      }
    },
    switchTab(index) {
      this.currentTab = index;
    },
    showFilter() {
      uni.showToast({
        title: '筛选功能开发中',
        icon: 'none'
      });
    },
    viewChatDetail(id) {
      // 跳转到聊天详情页
      uni.navigateTo({
        url: `/pages/chat/index?recordId=${id}`
      });
    },
    showDeleteConfirm() {
      this.deleteAllMode = true;
      this.showDeleteModal = true;
    },
    deleteChatRecord(id) {
      this.deleteAllMode = false;
      this.currentDeleteId = id;
      this.showDeleteModal = true;
    },
    cancelDelete() {
      this.showDeleteModal = false;
      this.deleteAllMode = false;
      this.currentDeleteId = null;
    },
    confirmDelete() {
      if (this.deleteAllMode) {
        // 删除所有记录
        this.chatRecords = [];
      } else if (this.currentDeleteId) {
        // 删除特定记录
        this.chatRecords = this.chatRecords.filter(record => record.id !== this.currentDeleteId);
      }
      
      this.showDeleteModal = false;
      this.deleteAllMode = false;
      this.currentDeleteId = null;
      
      // 显示提示
      uni.showToast({
        title: '删除成功',
        icon: 'success'
      });
    },
    shareChatRecord(id) {
      uni.showToast({
        title: '分享功能开发中',
        icon: 'none'
      });
    }
  }
}
</script>

<style>
.chat-history-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  background-color: #f5f5f5;
}

/* 状态栏样式 */
.status-bar {
  display: flex;
  justify-content: space-between;
  padding: 20rpx 30rpx;
  background-color: #fff;
}

.time {
  font-size: 32rpx;
  font-weight: 500;
}

.icons {
  display: flex;
  gap: 20rpx;
}

/* 页面头部样式 */
.page-header {
  display: flex;
  align-items: center;
  padding: 30rpx;
  border-bottom: 1rpx solid #eee;
  background-color: #fff;
}

.back-button {
  width: 70rpx;
  height: 70rpx;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  font-size: 36rpx;
  color: #333;
}

.page-title {
  flex: 1;
  font-size: 36rpx;
  font-weight: 600;
  text-align: center;
  margin-right: 70rpx;
}

.filter-button {
  width: 70rpx;
  height: 70rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f5f5f5;
  border-radius: 50%;
  color: #333;
}

/* 标签页样式 */
.tabs {
  display: flex;
  border-bottom: 1rpx solid #eee;
  background-color: #fff;
}

.tab {
  flex: 1;
  text-align: center;
  padding: 24rpx 0;
  font-size: 30rpx;
  color: #999;
  position: relative;
}

.tab.active {
  color: #2196f3;
  font-weight: 500;
}

.tab.active::after {
  content: "";
  display: block;
  width: 40rpx;
  height: 6rpx;
  background-color: #2196f3;
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  border-radius: 6rpx;
}

/* 搜索栏样式 */
.search-bar {
  display: flex;
  background-color: #f0f2f5;
  border-radius: 20rpx;
  padding: 20rpx 30rpx;
  margin: 30rpx;
  align-items: center;
}

.search-bar .fa {
  color: #999;
  margin-right: 20rpx;
  font-size: 30rpx;
}

.search-input {
  border: none;
  background: transparent;
  flex: 1;
  font-size: 28rpx;
  color: #333;
}

/* 主要内容区域样式 */
.content {
  flex: 1;
  padding: 0 30rpx;
}

/* 日期分隔符样式 */
.date-divider {
  text-align: center;
  color: #999;
  font-size: 28rpx;
  margin: 40rpx 0;
  position: relative;
}

.date-divider::before, .date-divider::after {
  content: "";
  display: block;
  width: 30%;
  height: 2rpx;
  background-color: #eee;
  position: absolute;
  top: 50%;
}

.date-divider::before {
  left: 5%;
}

.date-divider::after {
  right: 5%;
}

/* 聊天会话项样式 */
.chat-session {
  background-color: white;
  border-radius: 24rpx;
  margin-bottom: 30rpx;
  padding: 30rpx;
  box-shadow: 0 4rpx 10rpx rgba(0,0,0,0.05);
  position: relative;
}

.session-header {
  display: flex;
  align-items: center;
  margin-bottom: 20rpx;
}

.session-icon {
  width: 80rpx;
  height: 80rpx;
  border-radius: 50%;
  background-color: #f0f5ff;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 20rpx;
  color: #2196f3;
  font-size: 40rpx;
}

.session-info {
  flex: 1;
}

.session-title {
  font-size: 32rpx;
  font-weight: 500;
  color: #333;
  margin-bottom: 6rpx;
  display: block;
}

.session-time {
  font-size: 24rpx;
  color: #999;
}

.session-preview {
  color: #666;
  font-size: 28rpx;
  line-height: 1.5;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-overflow: ellipsis;
}

.session-actions {
  position: absolute;
  top: 30rpx;
  right: 30rpx;
  display: flex;
  gap: 20rpx;
}

.action-button {
  width: 60rpx;
  height: 60rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-size: 30rpx;
}

/* 空状态样式 */
.empty-state {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 60rpx;
}

.empty-icon {
  width: 200rpx;
  height: 200rpx;
  margin-bottom: 30rpx;
}

.empty-text {
  font-size: 32rpx;
  color: #999;
  margin-bottom: 40rpx;
}

.start-chat-btn {
  width: 300rpx;
  height: 80rpx;
  background-color: #2196f3;
  color: #fff;
  font-size: 32rpx;
  border-radius: 40rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* 弹窗样式 */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 100;
}

.modal-content {
  width: 80%;
  background-color: #fff;
  border-radius: 20rpx;
  padding: 40rpx;
}

.modal-title {
  font-size: 36rpx;
  font-weight: 600;
  color: #333;
  margin-bottom: 30rpx;
  text-align: center;
}

.modal-text {
  font-size: 30rpx;
  color: #666;
  margin-bottom: 40rpx;
  text-align: center;
}

.modal-actions {
  display: flex;
  justify-content: space-between;
}

.modal-btn {
  width: 45%;
  height: 80rpx;
  border-radius: 40rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 30rpx;
}

.cancel-btn {
  background-color: #f5f5f5;
  color: #666;
}

.confirm-btn {
  background-color: #ff6b6b;
  color: #fff;
}

/* 底部导航栏样式 */
.navbar {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: white;
  display: flex;
  justify-content: space-around;
  padding: 24rpx 0 60rpx;
  box-shadow: 0 -4rpx 20rpx rgba(0, 0, 0, 0.05);
  z-index: 100;
}

.nav-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  color: #999;
  font-size: 20rpx;
}

.nav-item .fa {
  font-size: 44rpx;
  margin-bottom: 8rpx;
}

.nav-item.active {
  color: #2196f3;
}

/* 字体图标样式 */
.fa {
  font-family: "FontAwesome";
}
.fa-chevron-left:before { content: "\f053"; }
.fa-filter:before { content: "\f0b0"; }
.fa-search:before { content: "\f002"; }
.fa-robot:before { content: "\f544"; }
.fa-book:before { content: "\f02d"; }
.fa-gamepad:before { content: "\f11b"; }
.fa-comment:before { content: "\f075"; }
.fa-share-alt:before { content: "\f1e0"; }
.fa-trash:before { content: "\f1f8"; }
.fa-home:before { content: "\f015"; }
.fa-compass:before { content: "\f14e"; }
.fa-user:before { content: "\f007"; }
.fa-signal:before { content: "\f012"; }
.fa-wifi:before { content: "\f1eb"; }
.fa-battery-full:before { content: "\f240"; }
</style> 