<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/* Global styles for Xiaozhi App */
	page {
		font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
		background-color: #f5f5f5;
		color: #212529;
		font-size: 28rpx;
		line-height: 1.5;
	}
	
	/* CSS Variables */
	:root {
		--primary-color: #4a90e2;
		--secondary-color: #f8f9fa;
		--accent-color: #fd7e14;
		--text-color: #212529;
		--light-text: #6c757d;
		--border-color: #dee2e6;
		--success-color: #28a745;
		--warning-color: #ffc107;
		--danger-color: #dc3545;
		--background-color: #f5f5f5;
	}
	
	/* Common Components */
	.card {
		background-color: #fff;
		border-radius: 24rpx;
		padding: 30rpx;
		margin-bottom: 30rpx;
		box-shadow: 0 2rpx 10rpx rgba(0,0,0,0.05);
	}
	
	.btn {
		display: inline-block;
		background-color: var(--primary-color);
		color: white;
		border: none;
		border-radius: 16rpx;
		padding: 24rpx 40rpx;
		font-size: 32rpx;
		font-weight: 500;
		text-align: center;
	}
	
	.btn-outline {
		background-color: transparent;
		border: 1px solid var(--primary-color);
		color: var(--primary-color);
	}
	
	.avatar {
		width: 100rpx;
		height: 100rpx;
		border-radius: 50%;
	}
	
	/* Typography */
	.header {
		font-size: 48rpx;
		font-weight: 600;
		margin-bottom: 30rpx;
	}
	
	.subheader {
		font-size: 36rpx;
		font-weight: 500;
		margin-bottom: 20rpx;
		color: var(--light-text);
	}
	
	/* Input fields */
	.input-group {
		margin-bottom: 30rpx;
	}
	
	.input {
		width: 100%;
		padding: 24rpx 30rpx;
		border: 1px solid var(--border-color);
		border-radius: 16rpx;
		font-size: 32rpx;
		box-sizing: border-box;
	}
	
	/* Status bar */
	.status-bar {
		height: 88rpx;
		width: 100%;
		background-color: #fff;
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 0 40rpx;
		box-sizing: border-box;
		font-size: 24rpx;
		color: #000;
		position: relative;
	}
	
	/* Navigation bar */
	.navbar {
		height: 166rpx;
		width: 100%;
		background-color: rgba(255, 255, 255, 0.9);
		position: fixed;
		bottom: 0;
		left: 0;
		display: flex;
		justify-content: space-around;
		align-items: center;
		border-top: 1px solid var(--border-color);
		z-index: 100;
	}
	
	.nav-item {
		display: flex;
		flex-direction: column;
		align-items: center;
		color: var(--light-text);
		font-size: 20rpx;
	}
	
	.nav-item.active {
		color: var(--primary-color);
	}
	
	.nav-item image {
		width: 48rpx;
		height: 48rpx;
		margin-bottom: 4rpx;
	}
	
	/* List items */
	.list-item {
		display: flex;
		align-items: center;
		padding: 24rpx 0;
		border-bottom: 1px solid var(--border-color);
	}
	
	.list-item:last-child {
		border-bottom: none;
	}
	
	.list-item-content {
		margin-left: 30rpx;
		flex: 1;
	}
	
	.list-item-title {
		font-weight: 500;
		margin-bottom: 6rpx;
	}
	
	.list-item-subtitle {
		font-size: 28rpx;
		color: var(--light-text);
	}
	
	/* Safe area for devices with notch */
	.safe-area-inset-bottom {
		padding-bottom: constant(safe-area-inset-bottom);
		padding-bottom: env(safe-area-inset-bottom);
	}
</style>
