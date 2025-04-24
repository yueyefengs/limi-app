<template>
	<view class="chat-container">
		<!-- Header -->
		<view class="header">
			<view class="avatar-container">
				<image class="ai-avatar" src="/static/images/ai-avatar.png" mode="aspectFill"></image>
				<view class="ai-status">在线</view>
			</view>
			<view class="ai-info">
				<text class="ai-name">小智AI</text>
				<text class="ai-description">你的AI学习伙伴</text>
			</view>
		</view>
		
		<!-- Chat Messages -->
		<scroll-view class="messages-container" scroll-y="true" :scroll-top="scrollTop" @scrolltolower="loadMoreMessages" ref="messagesScroll">
			<view class="date-divider" v-if="messages.length > 0">{{todayDate}}</view>
			
			<view v-for="(message, index) in messages" :key="index" class="message-wrapper" :class="message.type">
				<view class="message" :class="message.type">
					<view class="message-avatar" v-if="message.type === 'received'">
						<image src="/static/images/ai-avatar.png" mode="aspectFill"></image>
					</view>
					<view class="message-content">
						<text v-if="message.text" class="message-text">{{message.text}}</text>
						<view v-if="message.isImage" class="message-image">
							<image :src="message.url" mode="widthFix" @tap="previewImage(message.url)"></image>
						</view>
						<view v-if="message.isAudio" class="message-audio" @tap="playAudio(message.url)">
							<text class="audio-duration">{{message.duration}}″</text>
							<view class="audio-waves">
								<view class="wave" v-for="n in 4" :key="n"></view>
							</view>
							<text class="audio-status">{{isPlaying && currentAudio === message.url ? '播放中' : '点击播放'}}</text>
						</view>
					</view>
					<view class="message-avatar" v-if="message.type === 'sent'">
						<image :src="currentChild.avatar" mode="aspectFill"></image>
					</view>
					<view class="message-time">{{message.time}}</view>
				</view>
			</view>
			
			<!-- Typing indicator -->
			<view class="message-wrapper received" v-if="isTyping">
				<view class="message received">
					<view class="message-avatar">
						<image src="/static/images/ai-avatar.png" mode="aspectFill"></image>
					</view>
					<view class="message-content typing-indicator">
						<view class="dot"></view>
						<view class="dot"></view>
						<view class="dot"></view>
					</view>
				</view>
			</view>
		</scroll-view>
		
		<!-- Suggestion Chips -->
		<scroll-view class="suggestion-chips" scroll-x="true" v-if="suggestionChips.length > 0">
			<view 
				v-for="(chip, index) in suggestionChips" 
				:key="index" 
				class="chip" 
				@tap="sendMessage(chip)">
				{{chip}}
			</view>
		</scroll-view>
		
		<!-- Input Area -->
		<view class="input-container">
			<view class="input-buttons">
				<view class="input-button" @tap="toggleVoiceInput">
					<view class="icon" :class="{'active': isVoiceInputActive}">
						<view class="mic-icon"></view>
					</view>
				</view>
			</view>
			
			<view class="text-input" v-if="!isVoiceInputActive">
				<input 
					type="text" 
					v-model="inputMessage" 
					placeholder="输入消息..." 
					confirm-type="send"
					@confirm="sendTextMessage"
				/>
			</view>
			
			<view class="voice-input" v-else @touchstart="startRecording" @touchend="stopRecording">
				<text>按住说话</text>
			</view>
			
			<view class="input-buttons">
				<view class="input-button" v-if="inputMessage.trim().length > 0" @tap="sendTextMessage">
					<view class="icon send-icon">
						<view class="send-arrow"></view>
					</view>
				</view>
				<view class="input-button" v-else @tap="showMediaOptions">
					<view class="icon">
						<view class="plus-icon"></view>
					</view>
				</view>
			</view>
		</view>
		
		<!-- Media Options Popup -->
		<view class="media-options" v-if="showMedia">
			<view class="media-option" @tap="chooseImage">
				<view class="media-icon image-icon"></view>
				<text>图片</text>
			</view>
			<view class="media-option" @tap="takePhoto">
				<view class="media-icon camera-icon"></view>
				<text>拍照</text>
			</view>
			<view class="media-option" @tap="hideMediaOptions">
				<view class="media-icon close-icon"></view>
				<text>取消</text>
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
			suggestionChips: [
				'讲个故事吧',
				'我想学算数',
				'解释一下光合作用',
				'为什么天空是蓝色的？'
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
	},
	methods: {
		loadInitialMessages() {
			// Mock initial messages
			const initialMessages = [
				{
					type: 'received',
					text: '你好！我是小智AI，很高兴见到你！我可以陪你学习、回答问题、讲故事，你想聊些什么呢？',
					time: this.formatTime(new Date(Date.now() - 1000 * 60 * 5))
				}
			];
			this.messages = initialMessages;
			this.scrollToBottom();
		},
		
		loadMoreMessages() {
			// In a real app, this would load older messages from storage/API
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
			
			// Show typing indicator
			this.isTyping = true;
			this.scrollToBottom();
			
			// Simulate AI response after a delay
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
			
			// Generate new suggestions based on context
			this.updateSuggestions();
		},
		
		toggleVoiceInput() {
			this.isVoiceInputActive = !this.isVoiceInputActive;
			if (this.showMedia) this.showMedia = false;
		},
		
		startRecording() {
			this.isRecording = true;
			console.log('Recording started...');
			// In a real app, this would start the recording process
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
			
			// Simulate processing voice and getting text
			setTimeout(() => {
				// Simulate voice message
				const audioMessage = {
					type: 'sent',
					isAudio: true,
					url: '/static/audio/voice-message.mp3',
					duration: '3',
					time: this.formatTime(new Date())
				};
				
				this.messages.push(audioMessage);
				this.scrollToBottom();
				
				// Show typing indicator
				this.isTyping = true;
				this.scrollToBottom();
				
				// Simulate AI response after a delay
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
			
			// Show typing indicator
			this.isTyping = true;
			this.scrollToBottom();
			
			// Simulate AI response after a delay
			setTimeout(() => {
				this.receiveMessage({
					text: '我看到了你发送的图片！这张图片很有趣。你想让我解释一下图片中的内容吗？'
				});
			}, 2000);
		},
		
		playAudio(url) {
			if (this.isPlaying && this.currentAudio === url) {
				// Stop playing
				this.isPlaying = false;
				this.currentAudio = '';
				// In a real app, this would stop the audio
			} else {
				// Start playing
				this.isPlaying = true;
				this.currentAudio = url;
				// In a real app, this would play the audio
				
				// Simulate audio finishing
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
			// Use nextTick to ensure DOM is updated
			this.$nextTick(() => {
				const query = uni.createSelectorQuery().in(this);
				query.select('.messages-container').boundingClientRect();
				query.exec((res) => {
					if (res && res[0]) {
						this.scrollTop = res[0].height * 2; // Multiply by 2 to ensure it scrolls to bottom
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
			// Simple response logic - in a real app this would call an AI API
			const lowerMessage = userMessage.toLowerCase();
			
			if (lowerMessage.includes('故事')) {
				return {
					text: '从前有一只小兔子，它住在森林里的一个小洞穴里。每天早晨，它都会出去寻找食物。有一天，它发现了一片胡萝卜地...'
				};
			} else if (lowerMessage.includes('算数') || lowerMessage.includes('数学')) {
				return {
					text: '算数很有趣！我们来学习一些基础的加法吧。1+1=2, 2+2=4, 3+3=6。你想学习哪种算数题目呢？'
				};
			} else if (lowerMessage.includes('光合作用')) {
				return {
					text: '光合作用是植物利用阳光把二氧化碳和水转化为氧气和葡萄糖的过程。这就像植物的"做饭"过程，阳光是能源，二氧化碳和水是原料，而氧气是"烹饪"过程中释放的气体，葡萄糖则是植物的"食物"。'
				};
			} else if (lowerMessage.includes('天空') && lowerMessage.includes('蓝色')) {
				return {
					text: '天空看起来是蓝色的，是因为阳光中的蓝色光被空气中的小颗粒散射得最多。这些蓝色的光线从各个方向进入我们的眼睛，让我们看到的天空是蓝色的。这就像用一个蓝色的灯照亮一个房间一样！'
				};
			} else {
				return {
					text: '谢谢你的消息！你想了解什么其他有趣的知识呢？我可以帮你回答问题、讲故事，或者陪你玩一些有趣的学习游戏。'
				};
			}
		},
		
		updateSuggestions() {
			// Update suggestion chips based on conversation context
			// In a real app, this would be more sophisticated
			const lastMessage = this.messages[this.messages.length - 1];
			
			if (lastMessage && lastMessage.type === 'received') {
				if (lastMessage.text.includes('故事')) {
					this.suggestionChips = ['再讲一个故事', '这个故事有什么寓意？', '我想听冒险故事', '换个话题吧'];
				} else if (lastMessage.text.includes('算数') || lastMessage.text.includes('数学')) {
					this.suggestionChips = ['练习加法', '学习乘法', '数学游戏', '换个话题吧'];
				} else if (lastMessage.text.includes('光合作用')) {
					this.suggestionChips = ['植物怎么生长？', '动物呼吸是什么？', '为什么植物是绿色的？', '换个话题吧'];
				} else {
					this.suggestionChips = ['讲个故事吧', '我想学算数', '解释一下光合作用', '为什么天空是蓝色的？'];
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
	background-color: #F5F6F8;
}

.header {
	display: flex;
	align-items: center;
	padding: 15px;
	background-color: #FFFFFF;
	border-bottom: 1px solid #EAEAEA;
}

.avatar-container {
	position: relative;
	margin-right: 12px;
}

.ai-avatar {
	width: 50px;
	height: 50px;
	border-radius: 25px;
	background-color: #E1F5FE;
}

.ai-status {
	position: absolute;
	bottom: 0;
	right: 0;
	background-color: #4CAF50;
	color: white;
	font-size: 10px;
	padding: 2px 5px;
	border-radius: 10px;
}

.ai-info {
	display: flex;
	flex-direction: column;
}

.ai-name {
	font-size: 18px;
	font-weight: bold;
	color: #333333;
}

.ai-description {
	font-size: 12px;
	color: #999999;
}

.messages-container {
	flex: 1;
	padding: 15px;
	overflow-y: auto;
}

.date-divider {
	text-align: center;
	font-size: 12px;
	color: #999999;
	margin: 10px 0;
	padding: 5px;
}

.message-wrapper {
	margin-bottom: 15px;
	display: flex;
	flex-direction: column;
}

.message-wrapper.sent {
	align-items: flex-end;
}

.message-wrapper.received {
	align-items: flex-start;
}

.message {
	max-width: 70%;
	display: flex;
	flex-direction: row;
	position: relative;
}

.message-avatar {
	width: 36px;
	height: 36px;
	border-radius: 18px;
	overflow: hidden;
}

.message-avatar image {
	width: 100%;
	height: 100%;
}

.message-content {
	margin: 0 10px;
	padding: 10px 15px;
	border-radius: 18px;
	position: relative;
}

.message.sent .message-content {
	background-color: #DCF8C6;
	margin-right: 10px;
}

.message.received .message-content {
	background-color: #FFFFFF;
	margin-left: 10px;
}

.message-text {
	font-size: 16px;
	line-height: 1.4;
	color: #333333;
}

.message-time {
	font-size: 10px;
	color: #999999;
	margin-top: 5px;
	align-self: flex-end;
}

.message-image {
	width: 200px;
	border-radius: 12px;
	overflow: hidden;
}

.message-image image {
	width: 100%;
	height: auto;
}

.message-audio {
	display: flex;
	align-items: center;
	min-width: 120px;
	cursor: pointer;
}

.audio-duration {
	font-size: 14px;
	margin-right: 10px;
}

.audio-waves {
	display: flex;
	align-items: center;
}

.wave {
	width: 3px;
	height: 10px;
	background-color: #666;
	margin: 0 2px;
	border-radius: 5px;
}

.wave:nth-child(2) {
	height: 15px;
}

.wave:nth-child(3) {
	height: 8px;
}

.audio-status {
	font-size: 12px;
	color: #999;
	margin-left: 10px;
}

.typing-indicator {
	display: flex;
	align-items: center;
	justify-content: center;
	min-width: 50px;
	padding: 15px;
}

.dot {
	width: 8px;
	height: 8px;
	border-radius: 4px;
	background-color: #999999;
	margin: 0 3px;
	animation: bounce 1.5s infinite ease-in-out;
}

.dot:nth-child(1) {
	animation-delay: 0s;
}

.dot:nth-child(2) {
	animation-delay: 0.25s;
}

.dot:nth-child(3) {
	animation-delay: 0.5s;
}

@keyframes bounce {
	0%, 60%, 100% {
		transform: translateY(0);
	}
	30% {
		transform: translateY(-5px);
	}
}

.suggestion-chips {
	padding: 10px;
	white-space: nowrap;
	background-color: #FFFFFF;
	border-top: 1px solid #EAEAEA;
}

.chip {
	display: inline-block;
	padding: 8px 15px;
	margin-right: 10px;
	background-color: #E1F5FE;
	border-radius: 20px;
	font-size: 14px;
	color: #1976D2;
}

.input-container {
	display: flex;
	padding: 10px 15px;
	background-color: #FFFFFF;
	border-top: 1px solid #EAEAEA;
	align-items: center;
}

.input-buttons {
	display: flex;
	align-items: center;
}

.input-button {
	width: 36px;
	height: 36px;
	display: flex;
	align-items: center;
	justify-content: center;
}

.icon {
	width: 30px;
	height: 30px;
	border-radius: 15px;
	background-color: #F0F0F0;
	display: flex;
	align-items: center;
	justify-content: center;
}

.icon.active {
	background-color: #1976D2;
}

.mic-icon {
	width: 14px;
	height: 14px;
	border-radius: 7px;
	background-color: #666666;
	position: relative;
}

.mic-icon:after {
	content: '';
	position: absolute;
	bottom: -3px;
	left: 4px;
	width: 6px;
	height: 6px;
	background-color: #666666;
	border-radius: 3px;
}

.active .mic-icon,
.active .mic-icon:after {
	background-color: #FFFFFF;
}

.send-icon {
	background-color: #1976D2;
}

.send-arrow {
	width: 0;
	height: 0;
	border-top: 7px solid transparent;
	border-left: 10px solid white;
	border-bottom: 7px solid transparent;
}

.plus-icon {
	position: relative;
	width: 14px;
	height: 2px;
	background-color: #666666;
}

.plus-icon:after {
	content: '';
	position: absolute;
	top: -6px;
	left: 6px;
	width: 2px;
	height: 14px;
	background-color: #666666;
}

.text-input {
	flex: 1;
	margin: 0 10px;
	background-color: #F0F0F0;
	border-radius: 20px;
	padding: 0 15px;
	height: 40px;
}

.text-input input {
	width: 100%;
	height: 100%;
	font-size: 16px;
}

.voice-input {
	flex: 1;
	margin: 0 10px;
	background-color: #E1F5FE;
	border-radius: 20px;
	display: flex;
	align-items: center;
	justify-content: center;
	height: 40px;
	color: #1976D2;
	font-size: 16px;
}

.media-options {
	position: absolute;
	bottom: 70px;
	right: 15px;
	background-color: #FFFFFF;
	border-radius: 10px;
	box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
	padding: 10px;
}

.media-option {
	display: flex;
	align-items: center;
	padding: 10px;
	border-bottom: 1px solid #EAEAEA;
}

.media-option:last-child {
	border-bottom: none;
}

.media-icon {
	width: 30px;
	height: 30px;
	margin-right: 10px;
	background-color: #F0F0F0;
	border-radius: 15px;
	display: flex;
	align-items: center;
	justify-content: center;
}

.image-icon:before {
	content: '';
	width: 15px;
	height: 12px;
	border: 2px solid #666666;
	border-radius: 2px;
}

.camera-icon:before {
	content: '';
	width: 15px;
	height: 10px;
	border: 2px solid #666666;
	border-radius: 2px;
}

.camera-icon:after {
	content: '';
	position: absolute;
	width: 6px;
	height: 6px;
	border: 1px solid #666666;
	border-radius: 3px;
	background-color: #F0F0F0;
}

.close-icon:before {
	content: '';
	width: 2px;
	height: 15px;
	background-color: #666666;
	transform: rotate(45deg);
	position: absolute;
}

.close-icon:after {
	content: '';
	width: 2px;
	height: 15px;
	background-color: #666666;
	transform: rotate(-45deg);
	position: absolute;
}
</style> 