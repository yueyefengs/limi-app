<template>
  <view class="usage-report-container">
    <!-- Header -->
    <view class="header">
      <view class="back-button" @click="navigateBack">
        <image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
      </view>
      <text class="header-title">使用报告</text>
      <view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
    </view>
    
    <!-- Period Selector -->
    <view class="period-selector">
      <view 
        class="period-option" 
        v-for="(option, index) in periodOptions" 
        :key="index"
        :class="{ active: selectedPeriod === option.value }"
        @click="setPeriod(option.value)"
      >
        <text>{{ option.label }}</text>
      </view>
    </view>
    
    <!-- Report Content -->
    <scroll-view scroll-y class="report-content">
      <!-- Usage Summary Card -->
      <view class="report-card">
        <view class="card-header">
          <text class="card-title">使用概览</text>
          <view class="period-display">
            <text>{{ getDateRangeText() }}</text>
          </view>
        </view>
        
        <view class="summary-metrics">
          <view class="metric-item">
            <text class="metric-value">{{ formatTime(totalUsageTime) }}</text>
            <text class="metric-label">总使用时间</text>
          </view>
          <view class="metric-item">
            <text class="metric-value">{{ formatTime(dailyAverage) }}</text>
            <text class="metric-label">日均使用</text>
          </view>
          <view class="metric-item">
            <text class="metric-value">{{ usageDays }}</text>
            <text class="metric-label">使用天数</text>
          </view>
        </view>
        
        <view class="daily-usage-chart">
          <text class="chart-title">每日使用情况</text>
          <view class="chart-container">
            <view class="chart-bars">
              <view 
                class="chart-bar-column" 
                v-for="(day, index) in usageData" 
                :key="index"
              >
                <view class="bar-container">
                  <view 
                    class="usage-bar" 
                    :style="{ height: calculateBarHeight(day.minutes) + '%' }"
                    :class="{ 'over-limit': day.minutes > dailyTimeLimit }"
                  ></view>
                </view>
                <text class="day-label">{{ day.label }}</text>
                <text class="day-value">{{ formatTime(day.minutes) }}</text>
              </view>
            </view>
            
            <view class="limit-line" :style="{ bottom: calculateBarHeight(dailyTimeLimit) + '%' }">
              <text class="limit-label">每日限制 ({{ formatTime(dailyTimeLimit) }})</text>
            </view>
          </view>
        </view>
      </view>
      
      <!-- Content Usage Card -->
      <view class="report-card">
        <view class="card-header">
          <text class="card-title">内容使用分析</text>
        </view>
        
        <view class="content-distribution">
          <text class="distribution-title">内容类别分布</text>
          <view class="category-bars">
            <view 
              class="category-item" 
              v-for="(category, index) in contentCategories" 
              :key="index"
            >
              <view class="category-info">
                <view class="category-icon-wrapper" :style="{ backgroundColor: category.color }">
                  <image :src="category.icon" mode="aspectFit" class="category-icon"></image>
                </view>
                <text class="category-name">{{ category.name }}</text>
              </view>
              <view class="category-bar-container">
                <view 
                  class="category-bar" 
                  :style="{ width: category.percentage + '%', backgroundColor: category.color }"
                ></view>
                <text class="category-percentage">{{ category.percentage }}%</text>
              </view>
            </view>
          </view>
        </view>
        
        <view class="top-content">
          <text class="top-content-title">最常使用的内容</text>
          <view class="content-list">
            <view 
              class="content-item" 
              v-for="(item, index) in topContentItems" 
              :key="index"
            >
              <image :src="item.image" mode="aspectFill" class="content-image"></image>
              <view class="content-details">
                <text class="content-title">{{ item.title }}</text>
                <text class="content-category">{{ item.category }}</text>
                <view class="usage-info">
                  <text class="usage-time">{{ formatTime(item.minutes) }}</text>
                  <text class="usage-count">{{ item.sessions }}次</text>
                </view>
              </view>
            </view>
          </view>
        </view>
      </view>
      
      <!-- Time Pattern Card -->
      <view class="report-card">
        <view class="card-header">
          <text class="card-title">使用时间模式</text>
        </view>
        
        <view class="time-pattern">
          <text class="pattern-title">一天中的使用分布</text>
          <view class="time-chart">
            <view class="time-slots">
              <view 
                class="time-slot" 
                v-for="(slot, index) in timeSlots" 
                :key="index"
              >
                <view 
                  class="slot-bar" 
                  :style="{ height: slot.percentage + '%' }"
                  :class="{ 'most-active': slot.isHighest }"
                ></view>
                <text class="slot-label">{{ slot.label }}</text>
              </view>
            </view>
            
            <view class="peak-time">
              <text>最活跃时段: {{ getMostActiveTimeSlot() }}</text>
            </view>
          </view>
        </view>
        
        <view class="weekly-pattern">
          <text class="pattern-title">每周使用模式</text>
          <view class="week-stats">
            <view class="stat-row">
              <text class="stat-label">最活跃日期</text>
              <text class="stat-value">星期{{ getMostActiveDayOfWeek() }}</text>
            </view>
            <view class="stat-row">
              <text class="stat-label">最低使用日期</text>
              <text class="stat-value">星期{{ getLeastActiveDayOfWeek() }}</text>
            </view>
            <view class="stat-row">
              <text class="stat-label">工作日平均</text>
              <text class="stat-value">{{ formatTime(getWeekdayAverage()) }}</text>
            </view>
            <view class="stat-row">
              <text class="stat-label">周末平均</text>
              <text class="stat-value">{{ formatTime(getWeekendAverage()) }}</text>
            </view>
          </view>
        </view>
      </view>
      
      <!-- Achievements Card -->
      <view class="report-card">
        <view class="card-header">
          <text class="card-title">学习成就</text>
        </view>
        
        <view class="achievements">
          <view class="achievement-metrics">
            <view class="metric-item">
              <text class="metric-value">{{ completedLessons }}</text>
              <text class="metric-label">完成课程</text>
            </view>
            <view class="metric-item">
              <text class="metric-value">{{ learningStreak }}</text>
              <text class="metric-label">连续学习天数</text>
            </view>
            <view class="metric-item">
              <text class="metric-value">{{ newBadges }}</text>
              <text class="metric-label">新获得徽章</text>
            </view>
          </view>
          
          <view class="badges-section" v-if="recentBadges.length > 0">
            <text class="badges-title">最近获得的徽章</text>
            <view class="badges-list">
              <view 
                class="badge-item" 
                v-for="(badge, index) in recentBadges" 
                :key="index"
              >
                <image :src="badge.image" mode="aspectFit" class="badge-image"></image>
                <text class="badge-name">{{ badge.name }}</text>
                <text class="badge-date">{{ badge.date }}</text>
              </view>
            </view>
          </view>
        </view>
      </view>
      
      <!-- Download Report -->
      <view class="download-section">
        <button class="download-button" @click="downloadReport">
          <image src="/static/icons/download.png" mode="aspectFit" class="download-icon"></image>
          <text>下载完整报告</text>
        </button>
      </view>
    </scroll-view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      // Period selection
      selectedPeriod: 'week',
      periodOptions: [
        { label: '本周', value: 'week' },
        { label: '本月', value: 'month' },
        { label: '上月', value: 'lastMonth' },
        { label: '自定义', value: 'custom' }
      ],
      
      // Usage limits
      dailyTimeLimit: 120, // 2 hours in minutes
      
      // Summary metrics
      totalUsageTime: 735, // in minutes
      dailyAverage: 105, // in minutes
      usageDays: 7,
      
      // Daily usage data
      usageData: [
        { label: '周一', date: '06-10', minutes: 85 },
        { label: '周二', date: '06-11', minutes: 120 },
        { label: '周三', date: '06-12', minutes: 45 },
        { label: '周四', date: '06-13', minutes: 150 },
        { label: '周五', date: '06-14', minutes: 95 },
        { label: '周六', date: '06-15', minutes: 180 },
        { label: '周日', date: '06-16', minutes: 60 }
      ],
      
      // Content categories
      contentCategories: [
        { 
          name: '数学', 
          icon: '/static/icons/math.png', 
          percentage: 35,
          color: '#2196f3' 
        },
        { 
          name: '语文', 
          icon: '/static/icons/chinese.png', 
          percentage: 25,
          color: '#4caf50' 
        },
        { 
          name: '英语', 
          icon: '/static/icons/english.png', 
          percentage: 20,
          color: '#ff9800' 
        },
        { 
          name: '科学', 
          icon: '/static/icons/science.png', 
          percentage: 15,
          color: '#9c27b0' 
        },
        { 
          name: '艺术', 
          icon: '/static/icons/art.png', 
          percentage: 5,
          color: '#e91e63' 
        }
      ],
      
      // Top content
      topContentItems: [
        {
          title: '数学乐园 - 加法游戏',
          category: '数学',
          image: '/static/images/content-math.png',
          minutes: 120,
          sessions: 8
        },
        {
          title: '阅读故事 - 小红帽',
          category: '语文',
          image: '/static/images/content-chinese.png',
          minutes: 95,
          sessions: 5
        },
        {
          title: 'ABC英语启蒙',
          category: '英语',
          image: '/static/images/content-english.png',
          minutes: 70,
          sessions: 6
        }
      ],
      
      // Time patterns
      timeSlots: [
        { label: '6-8', percentage: 10, isHighest: false },
        { label: '8-10', percentage: 20, isHighest: false },
        { label: '10-12', percentage: 25, isHighest: false },
        { label: '12-14', percentage: 15, isHighest: false },
        { label: '14-16', percentage: 35, isHighest: true },
        { label: '16-18', percentage: 30, isHighest: false },
        { label: '18-20', percentage: 25, isHighest: false },
        { label: '20-22', percentage: 5, isHighest: false }
      ],
      
      // Achievement data
      completedLessons: 24,
      learningStreak: 7,
      newBadges: 3,
      recentBadges: [
        {
          name: '数学大师',
          image: '/static/images/badge-math.png',
          date: '2023-06-15'
        },
        {
          name: '阅读达人',
          image: '/static/images/badge-reading.png',
          date: '2023-06-13'
        },
        {
          name: '连续学习7天',
          image: '/static/images/badge-streak.png',
          date: '2023-06-16'
        }
      ]
    }
  },
  methods: {
    navigateBack() {
      uni.navigateBack();
    },
    
    setPeriod(period) {
      this.selectedPeriod = period;
      
      if (period === 'custom') {
        this.showDatePicker();
      } else {
        // In a real app, this would fetch data for the selected period
        // For demo purposes, we'll just keep the same data
      }
    },
    
    showDatePicker() {
      // Implement date range picker in a real app
      uni.showToast({
        title: '自定义日期功能开发中',
        icon: 'none'
      });
    },
    
    getDateRangeText() {
      const today = new Date();
      
      if (this.selectedPeriod === 'week') {
        const startOfWeek = new Date(today);
        startOfWeek.setDate(today.getDate() - today.getDay() + 1); // Monday
        const endOfWeek = new Date(today);
        endOfWeek.setDate(today.getDate() - today.getDay() + 7); // Sunday
        
        return `${startOfWeek.getMonth() + 1}月${startOfWeek.getDate()}日 - ${endOfWeek.getMonth() + 1}月${endOfWeek.getDate()}日`;
      } else if (this.selectedPeriod === 'month') {
        return `${today.getFullYear()}年${today.getMonth() + 1}月`;
      } else if (this.selectedPeriod === 'lastMonth') {
        const lastMonth = today.getMonth() === 0 ? 12 : today.getMonth();
        const year = today.getMonth() === 0 ? today.getFullYear() - 1 : today.getFullYear();
        return `${year}年${lastMonth}月`;
      } else {
        return '自定义时段';
      }
    },
    
    formatTime(minutes) {
      if (minutes < 60) {
        return `${minutes}分钟`;
      } else {
        const hours = Math.floor(minutes / 60);
        const mins = minutes % 60;
        return mins > 0 ? `${hours}小时${mins}分钟` : `${hours}小时`;
      }
    },
    
    calculateBarHeight(minutes) {
      // Maximum height is 100%
      // We'll use 150% of the daily limit as the maximum value
      const maxValue = this.dailyTimeLimit * 1.5;
      return Math.min((minutes / maxValue) * 100, 100);
    },
    
    getMostActiveTimeSlot() {
      const highestSlot = this.timeSlots.find(slot => slot.isHighest);
      return highestSlot ? highestSlot.label : '无数据';
    },
    
    getMostActiveDayOfWeek() {
      let maxUsage = 0;
      let maxDayIndex = 0;
      
      this.usageData.forEach((day, index) => {
        if (day.minutes > maxUsage) {
          maxUsage = day.minutes;
          maxDayIndex = index;
        }
      });
      
      const dayLabels = ['一', '二', '三', '四', '五', '六', '日'];
      return dayLabels[maxDayIndex];
    },
    
    getLeastActiveDayOfWeek() {
      let minUsage = Infinity;
      let minDayIndex = 0;
      
      this.usageData.forEach((day, index) => {
        if (day.minutes < minUsage) {
          minUsage = day.minutes;
          minDayIndex = index;
        }
      });
      
      const dayLabels = ['一', '二', '三', '四', '五', '六', '日'];
      return dayLabels[minDayIndex];
    },
    
    getWeekdayAverage() {
      // Weekdays are index 0-4 (Monday to Friday)
      const weekdayUsage = this.usageData.slice(0, 5);
      const total = weekdayUsage.reduce((sum, day) => sum + day.minutes, 0);
      return Math.round(total / weekdayUsage.length);
    },
    
    getWeekendAverage() {
      // Weekends are index 5-6 (Saturday and Sunday)
      const weekendUsage = this.usageData.slice(5, 7);
      const total = weekendUsage.reduce((sum, day) => sum + day.minutes, 0);
      return Math.round(total / weekendUsage.length);
    },
    
    downloadReport() {
      uni.showLoading({
        title: '正在生成报告...'
      });
      
      setTimeout(() => {
        uni.hideLoading();
        uni.showToast({
          title: '报告已下载',
          icon: 'success'
        });
      }, 2000);
    }
  }
}
</script>

<style>
.usage-report-container {
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

.period-selector {
  background-color: #fff;
  padding: 20rpx 40rpx;
  display: flex;
  overflow-x: auto;
  white-space: nowrap;
  border-bottom: 1rpx solid #f0f0f0;
}

.period-option {
  padding: 16rpx 30rpx;
  border-radius: 30rpx;
  margin-right: 20rpx;
  background-color: #f5f5f5;
}

.period-option text {
  font-size: 28rpx;
  color: #666;
}

.period-option.active {
  background-color: #2196f3;
}

.period-option.active text {
  color: white;
}

.report-content {
  padding: 30rpx;
  height: calc(100vh - 220rpx);
}

.report-card {
  background-color: #fff;
  border-radius: 16rpx;
  padding: 30rpx;
  margin-bottom: 30rpx;
  box-shadow: 0 2rpx 10rpx rgba(0,0,0,0.05);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30rpx;
}

.card-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
}

.period-display {
  font-size: 28rpx;
  color: #999;
}

/* Summary Metrics */
.summary-metrics {
  display: flex;
  justify-content: space-around;
  margin-bottom: 40rpx;
}

.metric-item {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.metric-value {
  font-size: 34rpx;
  font-weight: bold;
  color: #2196f3;
  margin-bottom: 10rpx;
}

.metric-label {
  font-size: 26rpx;
  color: #666;
}

/* Daily Usage Chart */
.daily-usage-chart {
  margin-top: 20rpx;
}

.chart-title {
  font-size: 28rpx;
  color: #333;
  margin-bottom: 20rpx;
  display: block;
}

.chart-container {
  height: 400rpx;
  position: relative;
}

.chart-bars {
  display: flex;
  justify-content: space-between;
  height: 350rpx;
}

.chart-bar-column {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 12%;
}

.bar-container {
  flex: 1;
  width: 100%;
  display: flex;
  align-items: flex-end;
  justify-content: center;
}

.usage-bar {
  width: 60%;
  background-color: #2196f3;
  border-radius: 8rpx 8rpx 0 0;
}

.usage-bar.over-limit {
  background-color: #ff5252;
}

.day-label {
  font-size: 24rpx;
  color: #666;
  margin-top: 10rpx;
  margin-bottom: 5rpx;
}

.day-value {
  font-size: 22rpx;
  color: #999;
}

.limit-line {
  position: absolute;
  left: 0;
  right: 0;
  height: 2rpx;
  background-color: #ff5252;
}

.limit-label {
  position: absolute;
  right: 0;
  top: -25rpx;
  font-size: 22rpx;
  color: #ff5252;
}

/* Content Distribution */
.content-distribution {
  margin-bottom: 40rpx;
}

.distribution-title, .top-content-title, .pattern-title, .badges-title {
  font-size: 28rpx;
  color: #333;
  margin-bottom: 20rpx;
  display: block;
}

.category-bars {
  margin-top: 30rpx;
}

.category-item {
  display: flex;
  align-items: center;
  margin-bottom: 20rpx;
}

.category-info {
  display: flex;
  align-items: center;
  width: 180rpx;
  margin-right: 20rpx;
}

.category-icon-wrapper {
  width: 50rpx;
  height: 50rpx;
  border-radius: 25rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 15rpx;
}

.category-icon {
  width: 30rpx;
  height: 30rpx;
}

.category-name {
  font-size: 26rpx;
  color: #333;
}

.category-bar-container {
  flex: 1;
  height: 30rpx;
  background-color: #f5f5f5;
  border-radius: 15rpx;
  position: relative;
  overflow: hidden;
}

.category-bar {
  height: 100%;
  border-radius: 15rpx;
}

.category-percentage {
  position: absolute;
  right: 15rpx;
  font-size: 24rpx;
  color: #fff;
  line-height: 30rpx;
}

/* Top Content */
.content-list {
  margin-top: 20rpx;
}

.content-item {
  display: flex;
  padding: 20rpx 0;
  border-bottom: 1rpx solid #f0f0f0;
}

.content-item:last-child {
  border-bottom: none;
}

.content-image {
  width: 120rpx;
  height: 120rpx;
  border-radius: 10rpx;
  margin-right: 20rpx;
}

.content-details {
  flex: 1;
}

.content-title {
  font-size: 28rpx;
  font-weight: 500;
  color: #333;
  margin-bottom: 10rpx;
}

.content-category {
  font-size: 24rpx;
  color: #999;
  margin-bottom: 15rpx;
}

.usage-info {
  display: flex;
  justify-content: space-between;
  max-width: 300rpx;
}

.usage-time, .usage-count {
  font-size: 24rpx;
  color: #666;
}

/* Time Pattern */
.time-pattern {
  margin-bottom: 40rpx;
}

.time-chart {
  height: 250rpx;
}

.time-slots {
  display: flex;
  justify-content: space-between;
  height: 200rpx;
  align-items: flex-end;
}

.time-slot {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 10%;
}

.slot-bar {
  width: 70%;
  background-color: #2196f3;
  border-radius: 8rpx 8rpx 0 0;
}

.slot-bar.most-active {
  background-color: #4caf50;
}

.slot-label {
  font-size: 22rpx;
  color: #999;
  margin-top: 10rpx;
}

.peak-time {
  text-align: right;
  margin-top: 15rpx;
}

.peak-time text {
  font-size: 24rpx;
  color: #4caf50;
}

/* Weekly Pattern */
.week-stats {
  background-color: #f9f9f9;
  border-radius: 10rpx;
  padding: 20rpx;
}

.stat-row {
  display: flex;
  justify-content: space-between;
  padding: 15rpx 0;
  border-bottom: 1rpx solid #f0f0f0;
}

.stat-row:last-child {
  border-bottom: none;
}

.stat-label {
  font-size: 26rpx;
  color: #666;
}

.stat-value {
  font-size: 26rpx;
  color: #333;
  font-weight: 500;
}

/* Achievements */
.achievement-metrics {
  display: flex;
  justify-content: space-around;
  margin-bottom: 40rpx;
}

.badges-section {
  margin-top: 20rpx;
}

.badges-list {
  display: flex;
  justify-content: space-around;
  margin-top: 20rpx;
}

.badge-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 30%;
}

.badge-image {
  width: 120rpx;
  height: 120rpx;
  margin-bottom: 15rpx;
}

.badge-name {
  font-size: 26rpx;
  color: #333;
  text-align: center;
  margin-bottom: 5rpx;
}

.badge-date {
  font-size: 22rpx;
  color: #999;
}

/* Download Button */
.download-section {
  margin: 30rpx 0;
}

.download-button {
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #2196f3;
  color: white;
  height: 90rpx;
  line-height: 90rpx;
  font-size: 30rpx;
  border-radius: 45rpx;
}

.download-icon {
  width: 36rpx;
  height: 36rpx;
  margin-right: 10rpx;
}
</style> 