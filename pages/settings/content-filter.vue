<template>
	<view class="content-filter-container">
		<!-- Header -->
		<view class="header">
			<view class="back-button" @click="navigateBack">
				<image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
			</view>
			<text class="header-title">内容过滤</text>
			<view style="width: 40rpx;"></view> <!-- Empty space for alignment -->
		</view>
		
		<!-- Intro Banner -->
		<view class="filter-banner">
			<image src="/static/images/content-filter.png" mode="aspectFit" class="banner-image"></image>
			<view class="banner-content">
				<text class="banner-title">为孩子提供安全内容</text>
				<text class="banner-description">根据孩子年龄和发展阶段，自定义内容过滤规则</text>
			</view>
		</view>
		
		<!-- Content -->
		<scroll-view scroll-y class="filter-content">
			<!-- Main Filter Toggle -->
			<view class="control-card">
				<view class="control-header">
					<text class="control-title">内容过滤</text>
					<switch :checked="isFilterEnabled" @change="toggleFilter" color="#2196f3" />
				</view>
				<view class="control-description">
					<text>开启后，系统将根据以下设置过滤不适合的内容</text>
				</view>
			</view>
			
			<!-- Age Appropriate Settings -->
			<view class="setting-card" v-if="isFilterEnabled">
				<view class="setting-header">
					<text class="setting-title">年龄适宜内容</text>
					<view class="age-selector">
						<view 
							class="age-option" 
							v-for="option in ageOptions" 
							:key="option.value"
							:class="{ active: selectedAge === option.value }"
							@click="setAgeOption(option.value)"
						>
							<text>{{ option.label }}</text>
						</view>
					</view>
				</view>
				<view class="setting-description">
					<text>{{ getAgeDescription() }}</text>
				</view>
			</view>
			
			<!-- Content Categories -->
			<view class="category-card" v-if="isFilterEnabled">
				<view class="category-header">
					<text class="category-title">内容分类设置</text>
				</view>
				
				<view class="category-list">
					<view class="category-item" v-for="(category, index) in contentCategories" :key="index">
						<view class="category-info">
							<image :src="category.icon" mode="aspectFit" class="category-icon"></image>
							<view class="category-text">
								<text class="category-name">{{ category.name }}</text>
								<text class="category-desc">{{ category.description }}</text>
							</view>
						</view>
						<switch 
							:checked="category.filtered" 
							@change="(e) => toggleCategory(index, e)" 
							color="#2196f3"
						/>
					</view>
				</view>
			</view>
			
			<!-- Sensitive Content Filter -->
			<view class="sensitive-card" v-if="isFilterEnabled">
				<view class="sensitive-header">
					<text class="sensitive-title">敏感内容过滤</text>
				</view>
				
				<view class="sensitive-topics">
					<view 
						class="topic-item" 
						v-for="(topic, index) in sensitiveTopics" 
						:key="index"
						@click="toggleTopic(index)"
					>
						<text class="topic-label">{{ topic.label }}</text>
						<view class="checkbox" :class="{ checked: topic.blocked }">
							<text class="check-mark" v-if="topic.blocked">✓</text>
						</view>
					</view>
				</view>
				
				<view class="filter-options">
					<button class="select-all" @click="selectAllTopics">全选</button>
					<button class="clear-all" @click="clearAllTopics">清除</button>
				</view>
			</view>
			
			<!-- Safe Search -->
			<view class="search-card" v-if="isFilterEnabled">
				<view class="search-header">
					<text class="search-title">安全搜索</text>
					<switch :checked="isSafeSearchEnabled" @change="toggleSafeSearch" color="#2196f3" />
				</view>
				<view class="search-description">
					<text>开启后，将过滤搜索结果中的不适宜内容</text>
				</view>
				
				<view class="search-levels" v-if="isSafeSearchEnabled">
					<view class="level-header">
						<text class="level-label">过滤严格程度</text>
					</view>
					<slider 
						:value="safeSearchLevel" 
						@change="changeSafeSearchLevel" 
						min="1" 
						max="3" 
						:step="1" 
						show-value 
						:activeColor="'#2196f3'"
					/>
					<view class="level-markers">
						<text class="marker">常规</text>
						<text class="marker">中等</text>
						<text class="marker">严格</text>
					</view>
					<view class="level-description">
						<text>{{ getSafeSearchDescription() }}</text>
					</view>
				</view>
			</view>
			
			<!-- Whitelist/Blacklist -->
			<view class="list-card" v-if="isFilterEnabled">
				<view class="list-header">
					<text class="list-title">自定义内容列表</text>
				</view>
				
				<view class="list-tabs">
					<view 
						class="tab" 
						:class="{ active: activeTab === 'whitelist' }" 
						@click="setActiveTab('whitelist')"
					>
						<text>允许列表</text>
					</view>
					<view 
						class="tab" 
						:class="{ active: activeTab === 'blacklist' }" 
						@click="setActiveTab('blacklist')"
					>
						<text>禁止列表</text>
					</view>
				</view>
				
				<view class="list-content">
					<view v-if="activeTab === 'whitelist'">
						<view class="list-description">
							<text>以下内容将始终允许显示，无论其他设置如何</text>
						</view>
						
						<view class="empty-list" v-if="whitelist.length === 0">
							<text class="empty-text">暂无允许的内容</text>
							<text class="empty-hint">点击下方按钮添加</text>
						</view>
						
						<view class="list-items" v-else>
							<view class="list-item" v-for="(item, index) in whitelist" :key="index">
								<text class="item-text">{{ item }}</text>
								<view class="delete-button" @click="removeFromWhitelist(index)">
									<text>移除</text>
								</view>
							</view>
						</view>
						
						<button class="add-button" @click="showAddWhitelistModal">添加允许内容</button>
					</view>
					
					<view v-if="activeTab === 'blacklist'">
						<view class="list-description">
							<text>以下内容将始终被禁止显示，无论其他设置如何</text>
						</view>
						
						<view class="empty-list" v-if="blacklist.length === 0">
							<text class="empty-text">暂无禁止的内容</text>
							<text class="empty-hint">点击下方按钮添加</text>
						</view>
						
						<view class="list-items" v-else>
							<view class="list-item" v-for="(item, index) in blacklist" :key="index">
								<text class="item-text">{{ item }}</text>
								<view class="delete-button" @click="removeFromBlacklist(index)">
									<text>移除</text>
								</view>
							</view>
						</view>
						
						<button class="add-button" @click="showAddBlacklistModal">添加禁止内容</button>
					</view>
				</view>
			</view>
			
			<!-- Save Button -->
			<view class="save-section">
				<button class="save-button" @click="saveSettings">保存设置</button>
			</view>
		</scroll-view>
		
		<!-- Add List Item Modal -->
		<view class="modal-overlay" v-if="showModal">
			<view class="modal-content">
				<view class="modal-header">
					<text class="modal-title">{{ activeTab === 'whitelist' ? '添加允许内容' : '添加禁止内容' }}</text>
					<view class="modal-close" @click="closeModal">
						<text>×</text>
					</view>
				</view>
				
				<view class="modal-body">
					<view class="input-group">
						<text class="input-label">{{ activeTab === 'whitelist' ? '内容名称或关键词' : '内容名称或关键词' }}</text>
						<input 
							type="text" 
							v-model="newListItem" 
							placeholder="请输入..."
							class="input-field"
						/>
					</view>
					
					<view class="modal-buttons">
						<button class="cancel-button" @click="closeModal">取消</button>
						<button class="confirm-button" @click="addToList">添加</button>
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
			isFilterEnabled: true,
			selectedAge: 'kids',
			ageOptions: [
				{ label: '幼儿', value: 'toddler', description: '仅显示专为2-4岁幼儿设计的内容，过滤所有可能引起不适的内容' },
				{ label: '儿童', value: 'kids', description: '适合5-8岁儿童的内容，过滤复杂、暴力和恐怖内容' },
				{ label: '少年', value: 'teen', description: '适合9-12岁儿童的内容，允许轻度挑战性内容，但过滤成人主题' },
				{ label: '青少年', value: 'teen_plus', description: '适合13岁以上青少年的内容，仅过滤明显不适合的内容' }
			],
			contentCategories: [
				{ 
					name: '暴力内容', 
					description: '包含打斗、武器、流血等画面',
					filtered: true,
					icon: '/static/icons/violence.png'
				},
				{ 
					name: '恐怖内容', 
					description: '可能引起恐惧或焦虑的内容',
					filtered: true,
					icon: '/static/icons/scary.png'
				},
				{ 
					name: '不当语言', 
					description: '包含粗俗、侮辱性或不适当的语言',
					filtered: true,
					icon: '/static/icons/language.png'
				},
				{ 
					name: '成人话题', 
					description: '涉及复杂社会问题或成人话题',
					filtered: true,
					icon: '/static/icons/adult.png'
				},
				{ 
					name: '商业内容', 
					description: '广告或促销内容',
					filtered: false,
					icon: '/static/icons/commercial.png'
				}
			],
			sensitiveTopics: [
				{ label: '战争与冲突', blocked: true },
				{ label: '自然灾害', blocked: false },
				{ label: '政治内容', blocked: true },
				{ label: '宗教内容', blocked: false },
				{ label: '社会问题', blocked: true },
				{ label: '恋爱关系', blocked: true },
				{ label: '疾病与医疗', blocked: false },
				{ label: '野生动物捕食', blocked: false },
				{ label: '恐怖/惊悚内容', blocked: true }
			],
			isSafeSearchEnabled: true,
			safeSearchLevel: 2,
			safeSearchDescriptions: [
				'基础过滤，仅阻止明显不适合儿童的内容',
				'中等过滤，阻止大多数不适合内容',
				'严格过滤，确保最高安全级别'
			],
			activeTab: 'whitelist',
			whitelist: ['儿童科学馆', '动物世界', '数学启蒙'],
			blacklist: ['僵尸', '怪物', '战争'],
			showModal: false,
			newListItem: ''
		}
	},
	methods: {
		navigateBack() {
			uni.navigateBack();
		},
		
		toggleFilter(e) {
			this.isFilterEnabled = e.detail.value;
		},
		
		setAgeOption(age) {
			this.selectedAge = age;
			
			// In a real app, you would adjust other settings based on the age selection
			// For example:
			if (age === 'toddler') {
				this.contentCategories.forEach(cat => cat.filtered = true);
				this.sensitiveTopics.forEach(topic => topic.blocked = true);
				this.safeSearchLevel = 3;
			} else if (age === 'teen_plus') {
				this.contentCategories.forEach(cat => {
					if (cat.name === '商业内容' || cat.name === '不当语言') {
						cat.filtered = false;
					}
				});
				this.sensitiveTopics.forEach((topic, index) => {
					if (index !== 8) { // Keep "恐怖/惊悚内容" blocked
						topic.blocked = false;
					}
				});
				this.safeSearchLevel = 1;
			}
		},
		
		getAgeDescription() {
			const option = this.ageOptions.find(opt => opt.value === this.selectedAge);
			return option ? option.description : '';
		},
		
		toggleCategory(index, event) {
			this.contentCategories[index].filtered = event.detail.value;
		},
		
		toggleTopic(index) {
			this.sensitiveTopics[index].blocked = !this.sensitiveTopics[index].blocked;
		},
		
		selectAllTopics() {
			this.sensitiveTopics.forEach(topic => {
				topic.blocked = true;
			});
		},
		
		clearAllTopics() {
			this.sensitiveTopics.forEach(topic => {
				topic.blocked = false;
			});
		},
		
		toggleSafeSearch(e) {
			this.isSafeSearchEnabled = e.detail.value;
		},
		
		changeSafeSearchLevel(e) {
			this.safeSearchLevel = e.detail.value;
		},
		
		getSafeSearchDescription() {
			return this.safeSearchDescriptions[this.safeSearchLevel - 1];
		},
		
		setActiveTab(tab) {
			this.activeTab = tab;
		},
		
		removeFromWhitelist(index) {
			this.whitelist.splice(index, 1);
		},
		
		removeFromBlacklist(index) {
			this.blacklist.splice(index, 1);
		},
		
		showAddWhitelistModal() {
			this.activeTab = 'whitelist';
			this.newListItem = '';
			this.showModal = true;
		},
		
		showAddBlacklistModal() {
			this.activeTab = 'blacklist';
			this.newListItem = '';
			this.showModal = true;
		},
		
		closeModal() {
			this.showModal = false;
			this.newListItem = '';
		},
		
		addToList() {
			if (this.newListItem.trim()) {
				if (this.activeTab === 'whitelist') {
					this.whitelist.push(this.newListItem.trim());
				} else {
					this.blacklist.push(this.newListItem.trim());
				}
				this.newListItem = '';
				this.showModal = false;
			} else {
				uni.showToast({
					title: '请输入内容',
					icon: 'none'
				});
			}
		},
		
		saveSettings() {
			// In a real app, you would save these settings to storage or server
			uni.showLoading({
				title: '保存中...'
			});
			
			setTimeout(() => {
				uni.hideLoading();
				uni.showToast({
					title: '设置已保存',
					icon: 'success'
				});
				
				// Navigate back
				setTimeout(() => {
					uni.navigateBack();
				}, 1500);
			}, 1000);
		}
	}
}
</script>

<style>
.content-filter-container {
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

.filter-banner {
	background: linear-gradient(to right, #e3f2fd, #bbdefb);
	padding: 30rpx 40rpx;
	display: flex;
	align-items: center;
}

.banner-image {
	width: 100rpx;
	height: 100rpx;
	margin-right: 30rpx;
}

.banner-content {
	flex: 1;
}

.banner-title {
	font-size: 32rpx;
	font-weight: bold;
	color: #1565c0;
	margin-bottom: 10rpx;
	display: block;
}

.banner-description {
	font-size: 26rpx;
	color: #1976d2;
}

.filter-content {
	padding: 30rpx;
	height: calc(100vh - 300rpx);
}

.control-card, .setting-card, .category-card, .sensitive-card, .search-card, .list-card {
	background-color: #fff;
	border-radius: 16rpx;
	padding: 30rpx;
	margin-bottom: 30rpx;
	box-shadow: 0 2rpx 10rpx rgba(0,0,0,0.05);
}

.control-header, .setting-header, .category-header, .sensitive-header, .search-header, .list-header {
	display: flex;
	justify-content: space-between;
	align-items: center;
	margin-bottom: 20rpx;
}

.control-title, .setting-title, .category-title, .sensitive-title, .search-title, .list-title {
	font-size: 32rpx;
	font-weight: bold;
	color: #333;
}

.control-description, .setting-description, .search-description, .list-description {
	font-size: 26rpx;
	color: #666;
	margin-bottom: 20rpx;
}

/* Age Selector */
.age-selector {
	display: flex;
	margin: 20rpx 0;
	overflow-x: auto;
	white-space: nowrap;
	padding-bottom: 10rpx;
}

.age-option {
	padding: 12rpx 30rpx;
	border-radius: 30rpx;
	background-color: #f5f5f5;
	margin-right: 16rpx;
}

.age-option.active {
	background-color: #2196f3;
}

.age-option.active text {
	color: white;
}

.age-option text {
	font-size: 28rpx;
	color: #666;
}

/* Categories */
.category-list {
	margin-top: 30rpx;
}

.category-item {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 20rpx 0;
	border-bottom: 1rpx solid #f0f0f0;
}

.category-item:last-child {
	border-bottom: none;
}

.category-info {
	display: flex;
	align-items: center;
	flex: 1;
}

.category-icon {
	width: 50rpx;
	height: 50rpx;
	margin-right: 20rpx;
}

.category-text {
	flex: 1;
}

.category-name {
	font-size: 28rpx;
	color: #333;
	margin-bottom: 6rpx;
	display: block;
}

.category-desc {
	font-size: 24rpx;
	color: #999;
	display: block;
}

/* Sensitive Topics */
.sensitive-topics {
	display: flex;
	flex-wrap: wrap;
	margin: 20rpx 0;
}

.topic-item {
	width: 33.33%;
	padding: 16rpx;
	box-sizing: border-box;
	display: flex;
	flex-direction: column;
	align-items: center;
}

.topic-label {
	font-size: 26rpx;
	color: #333;
	text-align: center;
	margin-bottom: 16rpx;
}

.checkbox {
	width: 40rpx;
	height: 40rpx;
	border: 2rpx solid #ccc;
	border-radius: 8rpx;
	display: flex;
	align-items: center;
	justify-content: center;
}

.checkbox.checked {
	background-color: #2196f3;
	border-color: #2196f3;
}

.check-mark {
	color: white;
	font-size: 28rpx;
}

.filter-options {
	display: flex;
	justify-content: space-between;
	margin-top: 30rpx;
}

.select-all, .clear-all {
	width: 48%;
	height: 80rpx;
	line-height: 80rpx;
	font-size: 28rpx;
	border-radius: 40rpx;
}

.select-all {
	background-color: #2196f3;
	color: white;
}

.clear-all {
	background-color: #f5f5f5;
	color: #666;
}

/* Safe Search */
.search-levels {
	margin-top: 30rpx;
}

.level-header {
	margin-bottom: 20rpx;
}

.level-label {
	font-size: 28rpx;
	color: #333;
}

.level-markers {
	display: flex;
	justify-content: space-between;
	margin-top: 10rpx;
}

.marker {
	font-size: 24rpx;
	color: #999;
}

.level-description {
	margin-top: 20rpx;
	padding: 20rpx;
	background-color: #f5f5f5;
	border-radius: 8rpx;
}

.level-description text {
	font-size: 26rpx;
	color: #666;
}

/* List Tabs */
.list-tabs {
	display: flex;
	border-bottom: 1rpx solid #eee;
	margin-bottom: 30rpx;
}

.tab {
	padding: 20rpx 0;
	flex: 1;
	text-align: center;
}

.tab text {
	font-size: 28rpx;
	color: #666;
	padding-bottom: 20rpx;
}

.tab.active text {
	color: #2196f3;
	border-bottom: 4rpx solid #2196f3;
	padding-bottom: 16rpx;
}

.empty-list {
	display: flex;
	flex-direction: column;
	align-items: center;
	padding: 40rpx 0;
}

.empty-text {
	font-size: 30rpx;
	color: #333;
	margin-bottom: 10rpx;
}

.empty-hint {
	font-size: 26rpx;
	color: #999;
}

.list-items {
	margin-bottom: 30rpx;
}

.list-item {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 20rpx;
	background-color: #f5f5f5;
	border-radius: 8rpx;
	margin-bottom: 16rpx;
}

.item-text {
	font-size: 28rpx;
	color: #333;
}

.delete-button {
	padding: 8rpx 20rpx;
	background-color: #ff5252;
	border-radius: 30rpx;
}

.delete-button text {
	font-size: 24rpx;
	color: white;
}

.add-button {
	background-color: #f5f5f5;
	border: 2rpx dashed #ccc;
	height: 90rpx;
	line-height: 90rpx;
	border-radius: 8rpx;
	font-size: 28rpx;
	color: #666;
}

/* Save Section */
.save-section {
	padding: 20rpx 0;
	margin-bottom: 30rpx;
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
	padding-bottom: 20rpx;
	border-bottom: 1rpx solid #eee;
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

.modal-body {
	padding-bottom: 20rpx;
}

.input-group {
	margin-bottom: 30rpx;
}

.input-label {
	font-size: 28rpx;
	color: #333;
	margin-bottom: 16rpx;
	display: block;
}

.input-field {
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