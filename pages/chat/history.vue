<template>
  <view class="chat-history-container">
    <!-- Header -->
    <view class="header">
      <view class="header-left" @click="navigateBack">
        <image class="back-icon" src="/static/icons/back.png" mode="aspectFit"></image>
      </view>
      <view class="header-title">聊天记录</view>
      <view class="header-right">
        <image v-if="chatRecords.length > 0" class="delete-icon" src="/static/icons/delete.png" mode="aspectFit" @click="showDeleteConfirm"></image>
      </view>
    </view>

    <!-- Empty State -->
    <view v-if="chatRecords.length === 0" class="empty-state">
      <image class="empty-icon" src="/static/icons/empty-chat.png" mode="aspectFit"></image>
      <text class="empty-text">暂无聊天记录</text>
      <button class="start-chat-btn" @click="navigateToChat">开始聊天</button>
    </view>

    <!-- Chat History List -->
    <scroll-view v-else class="chat-list" scroll-y="true">
      <view 
        v-for="(record, index) in chatRecords" 
        :key="index" 
        class="chat-item"
        @click="viewChatDetail(record.id)"
      >
        <view class="chat-item-header">
          <view class="chat-date">{{formatDate(record.date)}}</view>
          <view class="chat-time">{{formatTime(record.date)}}</view>
        </view>
        <view class="chat-preview">
          <text class="chat-preview-text">{{record.lastMessage}}</text>
        </view>
        <view class="chat-meta">
          <text class="message-count">{{record.messageCount}}条消息</text>
          <view class="chat-actions">
            <view class="icon-wrapper" @click.stop="shareChatRecord(record.id)">
              <image class="action-icon" src="/static/icons/share.png" mode="aspectFit"></image>
            </view>
            <view class="icon-wrapper" @click.stop="deleteChatRecord(record.id)">
              <image class="action-icon" src="/static/icons/trash.png" mode="aspectFit"></image>
            </view>
          </view>
        </view>
      </view>
    </scroll-view>

    <!-- Delete Confirmation Modal -->
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
  </view>
</template>

<script>
export default {
  data() {
    return {
      chatRecords: [],
      showDeleteModal: false,
      deleteAllMode: false,
      currentDeleteId: null
    }
  },
  onLoad() {
    this.loadChatRecords()
  },
  methods: {
    navigateBack() {
      uni.navigateBack()
    },
    navigateToChat() {
      uni.navigateTo({
        url: '/pages/chat/index'
      })
    },
    loadChatRecords() {
      // In a real app, load from storage or API
      // This is mock data for demonstration
      this.chatRecords = [
        {
          id: '1',
          date: new Date(new Date().getTime() - 24 * 60 * 60 * 1000),
          lastMessage: '我想了解更多关于恐龙的知识，能告诉我一些有趣的事实吗？',
          messageCount: 12
        },
        {
          id: '2',
          date: new Date(new Date().getTime() - 2 * 24 * 60 * 60 * 1000),
          lastMessage: '太空是什么样子的？宇航员在太空中是怎么生活的？',
          messageCount: 8
        },
        {
          id: '3',
          date: new Date(new Date().getTime() - 5 * 24 * 60 * 60 * 1000),
          lastMessage: '为什么天空是蓝色的？为什么有时候会变成红色或橙色？',
          messageCount: 15
        }
      ]
    },
    formatDate(date) {
      const now = new Date()
      const today = new Date(now.getFullYear(), now.getMonth(), now.getDate())
      const yesterday = new Date(today)
      yesterday.setDate(yesterday.getDate() - 1)
      
      if (date.getDate() === now.getDate() && 
          date.getMonth() === now.getMonth() && 
          date.getFullYear() === now.getFullYear()) {
        return '今天'
      } else if (date.getDate() === yesterday.getDate() && 
                date.getMonth() === yesterday.getMonth() && 
                date.getFullYear() === yesterday.getFullYear()) {
        return '昨天'
      } else {
        return `${date.getFullYear()}-${(date.getMonth()+1).toString().padStart(2, '0')}-${date.getDate().toString().padStart(2, '0')}`
      }
    },
    formatTime(date) {
      return `${date.getHours().toString().padStart(2, '0')}:${date.getMinutes().toString().padStart(2, '0')}`
    },
    viewChatDetail(id) {
      // Navigate to chat replay view with the specific chat ID
      uni.navigateTo({
        url: `/pages/chat/index?recordId=${id}`
      })
    },
    showDeleteConfirm() {
      this.deleteAllMode = true
      this.showDeleteModal = true
    },
    deleteChatRecord(id) {
      this.deleteAllMode = false
      this.currentDeleteId = id
      this.showDeleteModal = true
    },
    cancelDelete() {
      this.showDeleteModal = false
      this.deleteAllMode = false
      this.currentDeleteId = null
    },
    confirmDelete() {
      if (this.deleteAllMode) {
        // Delete all records
        this.chatRecords = []
      } else if (this.currentDeleteId) {
        // Delete specific record
        this.chatRecords = this.chatRecords.filter(record => record.id !== this.currentDeleteId)
      }
      
      this.showDeleteModal = false
      this.deleteAllMode = false
      this.currentDeleteId = null
      
      // Show toast notification
      uni.showToast({
        title: '删除成功',
        icon: 'success'
      })
    },
    shareChatRecord(id) {
      uni.showToast({
        title: '分享功能开发中',
        icon: 'none'
      })
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

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 100rpx;
  padding: 0 30rpx;
  background-color: #ffffff;
  box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
  position: relative;
  z-index: 10;
}

.header-left, .header-right {
  width: 60rpx;
  height: 60rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.back-icon, .delete-icon {
  width: 40rpx;
  height: 40rpx;
}

.header-title {
  font-size: 36rpx;
  font-weight: 600;
  color: #333;
}

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
  background-color: #4e7df9;
  color: #fff;
  font-size: 32rpx;
  border-radius: 40rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.chat-list {
  flex: 1;
  padding: 20rpx 30rpx;
}

.chat-item {
  background-color: #fff;
  border-radius: 20rpx;
  padding: 30rpx;
  margin-bottom: 20rpx;
  box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
}

.chat-item-header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20rpx;
}

.chat-date {
  font-size: 28rpx;
  color: #333;
  font-weight: 600;
}

.chat-time {
  font-size: 26rpx;
  color: #999;
}

.chat-preview {
  margin-bottom: 20rpx;
}

.chat-preview-text {
  font-size: 28rpx;
  color: #666;
  line-height: 1.5;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
  overflow: hidden;
}

.chat-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.message-count {
  font-size: 24rpx;
  color: #999;
}

.chat-actions {
  display: flex;
}

.icon-wrapper {
  width: 60rpx;
  height: 60rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-left: 20rpx;
}

.action-icon {
  width: 36rpx;
  height: 36rpx;
}

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
</style> 