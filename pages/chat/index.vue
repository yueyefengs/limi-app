<template>
	<view class="chat-container">
		<!-- 状态栏 -->
		<view class="status-bar">
			<text class="time">9:41</text>
			<view class="icons">
				<text class="fa fa-signal"></text>
				<text class="fa fa-wifi"></text>
				<text class="fa fa-battery-full"></text>
			</view>
		</view>
		
		<!-- 聊天头部 -->
		<view class="chat-header">
			<view class="robot-avatar">
				<text class="fa fa-robot"></text>
			</view>
			<text class="chat-title">小智助手</text>
			<view class="chat-actions">
				<view class="chat-action-button" @click="navigateTo('/pages/chat/history')">
					<text class="fa fa-history"></text>
				</view>
				<view class="chat-action-button">
					<text class="fa fa-magic"></text>
				</view>
			</view>
		</view>
		
		<!-- 聊天消息 -->
		<scroll-view class="chat-messages" scroll-y="true" :scroll-top="scrollTop" @scrolltolower="loadMoreMessages" ref="messagesScroll">
			<view class="date-divider" v-if="messages.length > 0">{{todayDate}}</view>
			
			<view v-for="(message, index) in messages" :key="index" class="message" :class="message.type === 'received' ? 'bot' : 'user'">
				<view class="message-avatar" v-if="message.type === 'received'">
					<text class="fa fa-robot"></text>
				</view>
				<view v-if="message.type === 'received'">
					<view class="message-bubble">
						<text v-if="message.text" class="message-text">{{message.text}}</text>
						<view v-if="message.isImage" class="message-image">
							<image :src="message.url" mode="aspectFill" @tap="previewImage(message.url)"></image>
						</view>
						<view v-if="message.isAudio" class="message-audio" @tap="playAudio(message.url)">
							<text class="fa fa-play"></text>
							<view class="audio-wave"></view>
							<text class="audio-duration">{{message.duration}}″</text>
						</view>
					</view>
					<view class="action-chips" v-if="index === messages.length - 1 && message.type === 'received'">
						<view 
							v-for="(chip, chipIndex) in suggestionChips" 
							:key="chipIndex" 
							class="action-chip" 
							@tap="sendMessage(chip)">
							{{chip}}
						</view>
					</view>
				</view>
				<view v-else>
					<view class="message-bubble">
						<text v-if="message.text" class="message-text">{{message.text}}</text>
						<view v-if="message.isImage" class="message-image">
							<image :src="message.url" mode="aspectFill" @tap="previewImage(message.url)"></image>
						</view>
						<view v-if="message.isAudio" class="message-audio" @tap="playAudio(message.url)">
							<text class="fa fa-play"></text>
							<view class="audio-wave"></view>
							<text class="audio-duration">{{message.duration}}″</text>
						</view>
					</view>
				</view>
				<view class="message-avatar" v-if="message.type === 'sent'">
					<image :src="currentChild.avatar" mode="aspectFill"></image>
				</view>
			</view>
			
			<!-- 时间指示器 -->
			<view class="message-time">{{currentTime}}</view>
			
			<!-- 输入指示器 -->
			<view class="typing-indicator" v-if="isTyping">
				<view class="typing-dot"></view>
				<view class="typing-dot"></view>
				<view class="typing-dot"></view>
			</view>
		</scroll-view>
		
		<!-- 输入区域 -->
		<view class="chat-input-container">
			<view class="chat-action-button" @tap="showMediaOptions">
				<text class="fa fa-plus"></text>
			</view>
			
			<input 
				type="text" 
				class="chat-input" 
				v-model="inputMessage" 
				placeholder="输入消息..." 
				confirm-type="send"
				@confirm="sendTextMessage"
			/>
			
			<view class="chat-action-button" @tap="toggleVoiceInput">
				<view class="voice-button">
					<text class="fa fa-microphone"></text>
				</view>
			</view>
			
			<view class="chat-send" v-if="inputMessage.trim().length > 0" @tap="sendTextMessage">
				<text class="fa fa-paper-plane"></text>
			</view>
		</view>
		
		<!-- 媒体选项 -->
		<view class="media-options" v-if="showMedia">
			<view class="media-option" @tap="chooseImage">
				<view class="media-icon image-icon">
					<text class="fa fa-image"></text>
				</view>
				<text>图片</text>
			</view>
			<view class="media-option" @tap="takePhoto">
				<view class="media-icon camera-icon">
					<text class="fa fa-camera"></text>
				</view>
				<text>拍照</text>
			</view>
			<view class="media-option" @tap="hideMediaOptions">
				<view class="media-icon close-icon">
					<text class="fa fa-times"></text>
				</view>
				<text>取消</text>
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
			currentChild: {
				name: '小明',
				avatar: '/static/images/child-avatar.png',
				age: 5
			},
			messages: [],
			inputMessage: '',
			isVoiceInputActive: false,
			isRecording: false,
			isTyping: false,
			scrollTop: 0,
			showMedia: false,
			isPlaying: false,
			currentAudio: '',
			currentTime: '09:41',
			suggestionChips: [
				'讲个恐龙故事',
				'来首儿歌',
				'猜谜语',
				'学习动物知识'
			]
		};
	},
	computed: {
		todayDate() {
			const today = new Date();
			return `${today.getFullYear()}年${today.getMonth() + 1}月${today.getDate()}日`;
		}
	},
	onLoad() {
		this.loadInitialMessages();
		this.updateCurrentTime();
		setInterval(this.updateCurrentTime, 60000);
	},
	methods: {
		updateCurrentTime() {
			const now = new Date();
			const hours = now.getHours().toString().padStart(2, '0');
			const minutes = now.getMinutes().toString().padStart(2, '0');
			this.currentTime = `今天 ${hours}:${minutes}`;
		},
		navigateTo(url) {
			uni.switchTab({
				url: url
			});
		},
		loadInitialMessages() {
			// 模拟初始消息
			const initialMessages = [
				{
					type: 'received',
					text: '你好！我是小智，今天想学些什么呢？我可以给你讲故事、唱儿歌、玩益智游戏，或者回答简单的问题！',
					time: this.formatTime(new Date(Date.now() - 1000 * 60 * 5))
				}
			];
			this.messages = initialMessages;
			this.scrollToBottom();
		},
		
		loadMoreMessages() {
			// 加载更多消息
			console.log('Loading more messages...');
		},
		
		sendTextMessage() {
			if (!this.inputMessage.trim()) return;
			
			const newMessage = {
				type: 'sent',
				text: this.inputMessage,
				time: this.formatTime(new Date())
			};
			
			this.messages.push(newMessage);
			this.scrollToBottom();
			
			const userMessage = this.inputMessage;
			this.inputMessage = '';
			
			// 显示输入指示器
			this.isTyping = true;
			this.scrollToBottom();
			
			// 模拟AI响应
			setTimeout(() => {
				this.receiveMessage(this.generateResponse(userMessage));
			}, 1500);
		},
		
		sendMessage(text) {
			this.inputMessage = text;
			this.sendTextMessage();
		},
		
		receiveMessage(messageData) {
			this.isTyping = false;
			
			const newMessage = {
				type: 'received',
				...messageData,
				time: this.formatTime(new Date())
			};
			
			this.messages.push(newMessage);
			this.scrollToBottom();
			
			// 根据上下文生成新的建议
			this.updateSuggestions();
		},
		
		toggleVoiceInput() {
			this.isVoiceInputActive = !this.isVoiceInputActive;
			if (this.showMedia) this.showMedia = false;
			
			if (this.isVoiceInputActive) {
				this.startRecording();
			} else {
				this.stopRecording();
			}
		},
		
		startRecording() {
			this.isRecording = true;
			console.log('Recording started...');
			uni.showToast({
				title: '录音中...',
				icon: 'none',
				duration: 10000
			});
		},
		
		stopRecording() {
			if (!this.isRecording) return;
			
			this.isRecording = false;
			console.log('Recording stopped');
			uni.hideToast();
			
			// 模拟语音消息
			setTimeout(() => {
				const audioMessage = {
					type: 'sent',
					isAudio: true,
					url: '/static/audio/voice-message.mp3',
					duration: '0:48',
					time: this.formatTime(new Date())
				};
				
				this.messages.push(audioMessage);
				this.scrollToBottom();
				
				// 显示输入指示器
				this.isTyping = true;
				this.scrollToBottom();
				
				// 模拟AI响应
				setTimeout(() => {
					this.receiveMessage(this.generateResponse('语音消息内容'));
				}, 1500);
			}, 500);
		},
		
		showMediaOptions() {
			this.showMedia = true;
		},
		
		hideMediaOptions() {
			this.showMedia = false;
		},
		
		chooseImage() {
			uni.chooseImage({
				count: 1,
				success: (res) => {
					this.sendImageMessage(res.tempFilePaths[0]);
					this.hideMediaOptions();
				}
			});
		},
		
		takePhoto() {
			uni.chooseImage({
				count: 1,
				sourceType: ['camera'],
				success: (res) => {
					this.sendImageMessage(res.tempFilePaths[0]);
					this.hideMediaOptions();
				}
			});
		},
		
		sendImageMessage(imageUrl) {
			const imageMessage = {
				type: 'sent',
				isImage: true,
				url: imageUrl,
				time: this.formatTime(new Date())
			};
			
			this.messages.push(imageMessage);
			this.scrollToBottom();
			
			// 显示输入指示器
			this.isTyping = true;
			this.scrollToBottom();
			
			// 模拟AI响应
			setTimeout(() => {
				this.receiveMessage({
					text: '我看到了你发送的图片！这张图片很有趣。你想让我解释一下图片中的内容吗？',
					isImage: true,
					url: imageUrl
				});
			}, 2000);
		},
		
		playAudio(url) {
			if (this.isPlaying && this.currentAudio === url) {
				// 停止播放
				this.isPlaying = false;
				this.currentAudio = '';
			} else {
				// 开始播放
				this.isPlaying = true;
				this.currentAudio = url;
				
				// 模拟音频结束
				setTimeout(() => {
					this.isPlaying = false;
					this.currentAudio = '';
				}, 3000);
			}
		},
		
		previewImage(url) {
			uni.previewImage({
				urls: [url]
			});
		},
		
		scrollToBottom() {
			this.$nextTick(() => {
				const query = uni.createSelectorQuery().in(this);
				query.select('.chat-messages').boundingClientRect();
				query.exec((res) => {
					if (res && res[0]) {
						this.scrollTop = res[0].height * 2;
					}
				});
			});
		},
		
		formatTime(date) {
			const hours = date.getHours().toString().padStart(2, '0');
			const minutes = date.getMinutes().toString().padStart(2, '0');
			return `${hours}:${minutes}`;
		},
		
		generateResponse(userMessage) {
			// 简单的响应逻辑
			const lowerMessage = userMessage.toLowerCase();
			
			if (lowerMessage.includes('恐龙') || lowerMessage.includes('故事')) {
				return {
					text: '好的！这是《小雷龙的大冒险》故事：从前，有一只名叫小雷龙的恐龙，他住在茂密的丛林里...',
					isAudio: true,
					url: '/static/audio/story.mp3',
					duration: '2:35'
				};
			} else if (lowerMessage.includes('儿歌') || lowerMessage.includes('唱歌')) {
				return {
					text: '好的，我来唱一首《小星星》儿歌：一闪一闪亮晶晶，满天都是小星星...',
					isAudio: true,
					url: '/static/audio/song.mp3',
					duration: '1:45'
				};
			} else if (lowerMessage.includes('谜语') || lowerMessage.includes('猜')) {
				return {
					text: '我来出一个谜语：什么东西有头无脚？猜猜看！'
				};
			} else if (lowerMessage.includes('动物') || lowerMessage.includes('知识')) {
				return {
					text: '你知道吗？大象是陆地上最大的动物，它们可以活到70岁，而且非常聪明！想了解哪种动物的知识呢？',
					isImage: true,
					url: '/static/images/elephant.jpg'
				};
			} else {
				return {
					text: '谢谢你的消息！你想了解什么有趣的知识呢？我可以给你讲故事、唱儿歌、玩益智游戏，或者回答简单的问题！'
				};
			}
		},
		
		updateSuggestions() {
			// 根据对话上下文更新建议
			const lastMessage = this.messages[this.messages.length - 1];
			
			if (lastMessage && lastMessage.type === 'received') {
				if (lastMessage.text.includes('恐龙') || lastMessage.text.includes('故事')) {
					this.suggestionChips = ['再讲一个故事', '讲讲霸王龙', '我想听冒险故事', '换个话题'];
				} else if (lastMessage.text.includes('儿歌') || lastMessage.text.includes('唱')) {
					this.suggestionChips = ['再唱一首', '我要听《两只老虎》', '学唱歌', '换个话题'];
				} else if (lastMessage.text.includes('谜语') || lastMessage.text.includes('猜')) {
					this.suggestionChips = ['再出一个谜语', '简单一点的', '我猜是枕头', '换个话题'];
				} else if (lastMessage.text.includes('动物')) {
					this.suggestionChips = ['讲讲长颈鹿', '海洋动物', '恐龙是什么动物', '换个话题'];
				} else {
					this.suggestionChips = ['讲个恐龙故事', '来首儿歌', '猜谜语', '学习动物知识'];
				}
			}
		}
	}
};
</script>

<style>
.chat-container {
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

/* 聊天头部样式 */
.chat-header {
	display: flex;
	align-items: center;
	padding: 20rpx 30rpx;
	border-bottom: 1rpx solid #eee;
	background-color: #fff;
}

.robot-avatar {
	width: 80rpx;
	height: 80rpx;
	border-radius: 50%;
	background-color: #f5f5f5;
	display: flex;
	align-items: center;
	justify-content: center;
	margin-right: 20rpx;
}

.robot-avatar .fa {
	color: #2196f3;
	font-size: 40rpx;
}

.chat-title {
	flex: 1;
	font-size: 36rpx;
	font-weight: 600;
	color: #333;
	text-align: center;
}

.chat-actions {
	display: flex;
}

.chat-action-button {
	width: 76rpx;
	height: 76rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	font-size: 36rpx;
	color: #666;
}

/* 聊天消息样式 */
.chat-messages {
	flex: 1;
	padding: 20rpx 30rpx;
	overflow-y: auto;
}

.date-divider {
	text-align: center;
	font-size: 24rpx;
	color: #999;
	margin: 20rpx 0;
	padding: 10rpx;
}

.message {
	margin-bottom: 30rpx;
	display: flex;
	align-items: flex-start;
}

.message.user {
	flex-direction: row-reverse;
}

.message-avatar {
	width: 72rpx;
	height: 72rpx;
	border-radius: 50%;
	margin: 0 20rpx;
	overflow: hidden;
	background-color: #f5f5f5;
	display: flex;
	align-items: center;
	justify-content: center;
}

.message-avatar .fa {
	color: #2196f3;
	font-size: 36rpx;
}

.message-avatar image {
	width: 100%;
	height: 100%;
}

.message-bubble {
	max-width: 70%;
	padding: 24rpx 30rpx;
	border-radius: 36rpx;
	font-size: 28rpx;
	position: relative;
}

.message.bot .message-bubble {
	background-color: #f0f2f5;
	color: #333;
	border-bottom-left-radius: 10rpx;
}

.message.user .message-bubble {
	background-color: #2196f3;
	color: white;
	border-bottom-right-radius: 10rpx;
}

.message-time {
	font-size: 22rpx;
	color: #999;
	margin: 10rpx 0;
	text-align: center;
}

.message-image {
	max-width: 400rpx;
	max-height: 400rpx;
	border-radius: 24rpx;
	overflow: hidden;
	margin-top: 10rpx;
}

.message-image image {
	width: 100%;
	height: 100%;
	object-fit: cover;
}

.message-audio {
	display: flex;
	align-items: center;
	min-width: 200rpx;
	margin-top: 10rpx;
}

.message-audio .fa {
	margin-right: 10rpx;
}

.audio-wave {
	flex: 1;
	height: 40rpx;
	background: linear-gradient(to bottom, #ddd 50%, transparent 50%);
	background-size: 8rpx 8rpx;
	margin: 0 10rpx;
}

.message.user .audio-wave {
	background: linear-gradient(to bottom, rgba(255,255,255,0.6) 50%, transparent 50%);
	background-size: 8rpx 8rpx;
}

.audio-duration {
	font-size: 24rpx;
	color: #999;
}

.message.user .audio-duration {
	color: rgba(255,255,255,0.8);
}

/* 建议芯片样式 */
.action-chips {
	display: flex;
	flex-wrap: wrap;
	margin-top: 20rpx;
	gap: 20rpx;
}

.action-chip {
	padding: 16rpx 24rpx;
	background-color: #edf1f5;
	border-radius: 30rpx;
	font-size: 26rpx;
	color: #2196f3;
}

/* 输入指示器样式 */
.typing-indicator {
	display: flex;
	padding: 12rpx 20rpx;
	align-items: center;
	margin-bottom: 30rpx;
}

.typing-dot {
	width: 16rpx;
	height: 16rpx;
	background-color: #999;
	border-radius: 50%;
	margin: 0 4rpx;
	animation: typing-animation 1.5s infinite ease-in-out;
}

.typing-dot:nth-child(2) {
	animation-delay: 0.2s;
}

.typing-dot:nth-child(3) {
	animation-delay: 0.4s;
}

@keyframes typing-animation {
	0% { transform: translateY(0); }
	50% { transform: translateY(-10rpx); }
	100% { transform: translateY(0); }
}

/* 输入区域样式 */
.chat-input-container {
	padding: 20rpx 30rpx;
	border-top: 1rpx solid #eee;
	background-color: white;
	display: flex;
	align-items: center;
}

.chat-input {
	flex: 1;
	border: 1rpx solid #eee;
	border-radius: 40rpx;
	padding: 20rpx 30rpx;
	font-size: 28rpx;
	background-color: #f5f5f5;
	margin: 0 20rpx;
}

.chat-send {
	width: 80rpx;
	height: 80rpx;
	border-radius: 50%;
	background-color: #2196f3;
	color: white;
	display: flex;
	align-items: center;
	justify-content: center;
	margin-left: 20rpx;
}

.chat-send .fa {
	font-size: 32rpx;
}

.voice-button {
	width: 50rpx;
	height: 50rpx;
	border-radius: 50%;
	background-color: #2196f3;
	color: white;
	display: flex;
	align-items: center;
	justify-content: center;
	font-size: 24rpx;
}

/* 媒体选项样式 */
.media-options {
	position: absolute;
	bottom: 140rpx;
	right: 30rpx;
	background-color: #FFFFFF;
	border-radius: 20rpx;
	box-shadow: 0 0 20rpx rgba(0, 0, 0, 0.1);
	padding: 20rpx;
	z-index: 100;
}

.media-option {
	display: flex;
	align-items: center;
	padding: 20rpx;
	border-bottom: 1rpx solid #EAEAEA;
}

.media-option:last-child {
	border-bottom: none;
}

.media-icon {
	width: 60rpx;
	height: 60rpx;
	margin-right: 20rpx;
	background-color: #F0F0F0;
	border-radius: 30rpx;
	display: flex;
	align-items: center;
	justify-content: center;
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
.fa-home:before { content: "\f015"; }
.fa-comment:before { content: "\f075"; }
.fa-compass:before { content: "\f14e"; }
.fa-user:before { content: "\f007"; }
.fa-robot:before { content: "\f544"; }
.fa-history:before { content: "\f1da"; }
.fa-magic:before { content: "\f0d0"; }
.fa-play:before { content: "\f04b"; }
.fa-plus:before { content: "\f067"; }
.fa-microphone:before { content: "\f130"; }
.fa-paper-plane:before { content: "\f1d8"; }
.fa-image:before { content: "\f03e"; }
.fa-camera:before { content: "\f030"; }
.fa-times:before { content: "\f00d"; }
.fa-signal:before { content: "\f012"; }
.fa-wifi:before { content: "\f1eb"; }
.fa-battery-full:before { content: "\f240"; }
</style> 