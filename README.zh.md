<p align="center">
<img src="http://7xqbzq.com1.z0.glb.clouddn.com/log.png" alt="ZFPlayer" title="ZFPlayer" width="557"/>
</p>

<p align="center">
<a href="https://travis-ci.org/renzifeng/ZFPlayer"><img src="https://travis-ci.org/renzifeng/ZFPlayer.svg?branch=master"></a>
<a href="https://img.shields.io/cocoapods/v/ZFPlayer.svg"><img src="https://img.shields.io/cocoapods/v/ZFPlayer.svg"></a>
<a href="https://img.shields.io/cocoapods/v/ZFPlayer.svg"><img src="https://img.shields.io/github/license/renzifeng/ZFPlayer.svg?style=flat"></a>
<a href="http://cocoadocs.org/docsets/ZFPlayer"><img src="https://img.shields.io/cocoapods/p/ZFPlayer.svg?style=flat"></a>
<a href="http://weibo.com/zifeng1300"><img src="https://img.shields.io/badge/weibo-@%E4%BB%BB%E5%AD%90%E4%B8%B0-yellow.svg?style=flat"></a>
</p>

基于AVPlayer，支持竖屏、横屏（横屏可锁定屏幕方向），上下滑动调节音量、屏幕亮度，左右滑动调节播放进度

[ZFPlayer剖析](http://www.jianshu.com/p/5566077bb25f)&emsp;&emsp;[哪些app使用ZFPlayer](http://www.jianshu.com/p/5fa55a05f87b)

## 特性
- [x] 支持横、竖屏切换，在横屏模式下可以锁定屏幕方向
- [x] 支持本地视频、网络视频播放
- [x] 支持在TableviewCell播放视频
- [x] 左侧1/2位置上下滑动调节屏幕亮度（模拟器调不了亮度，请在真机调试）
- [x] 右侧1/2位置上下滑动调节音量（模拟器调不了音量，请在真机调试）
- [x] 左右滑动调节播放进度
- [x] 全屏状态下拖动slider控制进度，显示视频的预览图
- [x] 断点下载功能
- [x] 切换视频分辨率

## 要求

- iOS 8+
- Xcode 8+


## 统计

哪些APP使用ZFPlayer，并上架AppStore，请告诉我，帮助我统计。

## 组件

- 断点下载: [ZFDownload](https://github.com/renzifeng/ZFDownload)
- 布局: Masonry


## 安装

### CocoaPods    

```ruby
pod 'ZFPlayer'
```

Then, run the following command:

```bash
$ pod install
```

## 使用 （支持IB和代码）
##### 设置状态栏颜色
请在info.plist中增加"View controller-based status bar appearance"字段，并改为NO

##### IB用法
直接拖UIView到IB上，宽高比为约束为16：9(优先级改为750，比1000低就行)，代码部分只需要实现

```objc
self.playerView.videoURL = self.videoURL;
// 设置代理
self.playerView.delegate = self;
```

`ZFPlayerDelegate`

```
/** 返回按钮事件 */
- (void)zf_playerBackAction;
/** 下载视频 */
- (void)zf_playerDownload:(NSString *)url;
```

##### 代码实现（Masonry）用法

```objc
self.playerView = [[ZFPlayerView alloc] init];
[self.view addSubview:self.playerView];
[self.playerView mas_makeConstraints:^(MASConstraintMaker *make) {
 	make.top.equalTo(self.view).offset(20);
 	make.left.right.equalTo(self.view);
	// 注意此处，宽高比16：9优先级比1000低就行，在因为iPhone 4S宽高比不是16：9
	make.height.equalTo(self.playerView.mas_width).multipliedBy(9.0f/16.0f).with.priority(750);
}];

// 指定控制层（可自定义）
ZFPlayerControlView *controlView = [[ZFPlayerControlView alloc] init];
self.playerView.controlView = controlView;

ZFPlayerModel *playerModel = [[ZFPlayerModel alloc]init];
playerModel.videoUrl = @"...";
// 设置视频model
self.playerView.playerModel = playerModel;
// 设置代理
self.playerView.delegate = self;
```

##### 设置视频的填充模式
```objc
 // 设置视频的填充模式，内部设置默认（ZFPlayerLayerGravityResizeAspect：等比例填充，直到一个维度到达区域边界）
 self.playerView.playerLayerGravity = ZFPlayerLayerGravityResizeAspect;
```

##### 是否有断点下载功能
```objc
 // 下载功能，如需要此功能设置这里
 self.playerView.hasDownload = YES;
```

##### 从xx秒开始播放视频
 ```objc
 // 从xx秒开始播放视频
 self.playerView.seekTime = 15;
 ```
 
##### 是否自动播放，默认不自动播放
```objc
// 是否自动播放，默认不自动播放
[self.playerView autoPlayTheVideo];
```

##### 设置播放前的占位图

```objc
// 设置播放前的占位图
ZFPlayerModel *playerModel = [[ZFPlayerModel alloc]init];
playerModel.placeholderImage = [UIImage imageNamed: @"..."];
self.playerView.playerModel = playerModel;

```

##### 自定义控制层
`self.playerView.controlView = 自定义的View;`

自定义view你需要实现以下方法，直接在`.m`中实现即可，可参考`ZFPlayerControlView.m`

```
/** 
 显示控制层
 */
- (void)zf_playerShowControlView;
/** 
 隐藏控制层
*/
- (void)zf_playerHideControlView;

/** 
 重置ControlView 
 */
- (void)zf_playerResetControlView;

/** 
 切换分辨率时重置ControlView 
 */
- (void)zf_playerResetControlViewForResolution;

/** 
 取消自动隐藏控制层view 
 */
- (void)zf_playerCancelAutoFadeOutControlView;

/** 
 播放完了 
 */
- (void)zf_playerPlayEnd;

/** 
 是否有下载功能 
 */
- (void)zf_playerHasDownloadFunction:(BOOL)sender;

/**
 是否有切换分辨率功能
 @param resolutionArray 分辨率名称的数组
 */
- (void)zf_playerResolutionArray:(NSArray *)resolutionArray;

/** 
 播放按钮状态 (播放、暂停状态)
 */
- (void)zf_playerPlayBtnState:(BOOL)state;

/** 
 锁定屏幕方向按钮状态 
 */
- (void)zf_playerLockBtnState:(BOOL)state;

/**
 下载按钮状态
 */
- (void)zf_playerDownloadBtnState:(BOOL)state;

/** 
 设置标题 
 */
- (void)zf_playerSetTitle:(NSString *)title;

/** 
 加载的菊花
 */
- (void)zf_playerActivity:(BOOL)animated;

/**
 设置预览图

 @param draggedTime 拖拽的时长
 @param image       预览图
 */
- (void)zf_playerDraggedTime:(NSInteger)draggedTime sliderImage:(UIImage *)image;

/**
 拖拽快进 快退

 @param draggedTime 拖拽的时长
 @param totalTime   视频总时长
 @param forawrd     是否是快进
 @param preview     是否有预览图
 */
- (void)zf_playerDraggedTime:(NSInteger)draggedTime totalTime:(NSInteger)totalTime isForward:(BOOL)forawrd hasPreview:(BOOL)preview;

/** 
 滑动调整进度结束结束 
 */
- (void)zf_playerDraggedEnd;

/**
 正常播放

 @param currentTime 当前播放时长
 @param totalTime   视频总时长
 @param value       slider的value(0.0~1.0)
 */
- (void)zf_playerCurrentTime:(NSInteger)currentTime totalTime:(NSInteger)totalTime sliderValue:(CGFloat)value;

/** 
 progress显示缓冲进度 
 */
- (void)zf_playerSetProgress:(CGFloat)progress;

/** 
 视频加载失败 
 */
- (void)zf_playerItemStatusFailed:(NSError *)error;

/**
 小屏播放
 */
- (void)zf_playerBottomShrinkPlay;

/**
 在cell播放
 */
- (void)zf_playerCellPlay;
```

### 图片效果演示

![图片效果演示](https://github.com/renzifeng/ZFPlayer/raw/master/screen.gif)

![声音调节演示](https://github.com/renzifeng/ZFPlayer/raw/master/volume.png)

![亮度调节演示](https://github.com/renzifeng/ZFPlayer/raw/master/brightness.png)

![进度调节预览图](https://github.com/renzifeng/ZFPlayer/raw/master/progress.png)

### 参考链接：

- [https://segmentfault.com/a/1190000004054258](https://segmentfault.com/a/1190000004054258)
- [http://sky-weihao.github.io/2015/10/06/Video-streaming-and-caching-in-iOS/](http://sky-weihao.github.io/2015/10/06/Video-streaming-and-caching-in-iOS/)
- [https://developer.apple.com/library/prerelease/ios/documentation/AudioVideo/Conceptual/AVFoundationPG/Articles/02_Playback.html#//apple_ref/doc/uid/TP40010188-CH3-SW8](https://developer.apple.com/library/prerelease/ios/documentation/AudioVideo/Conceptual/AVFoundationPG/Articles/02_Playback.html#//apple_ref/doc/uid/TP40010188-CH3-SW8)

---
### Swift版Player：
请移步 [BMPlayer](https://github.com/BrikerMan/BMPlayer)，感谢 BMPlayer 作者的开源。

### Swift版知乎日报：
本人最近编写的 [知乎日报Swift](https://github.com/renzifeng/ZFZhiHuDaily)。

---

# 联系我
- 微博: [@任子丰](https://weibo.com/zifeng1300)
- 邮箱: zifeng1300@gmail.com
- QQ群：213376937

# License

ZFPlayer is available under the MIT license. See the LICENSE file for more info.

