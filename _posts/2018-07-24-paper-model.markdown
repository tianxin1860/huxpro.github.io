---
layout:       post
title:        "Multi-Channel Information Crossing for Text Matching"
subtitle:     "阅读笔记"
date:         2018-07-24 20:00:00
author:       "田鑫"
header-img:   "img/in-post/post-eleme-pwa/eleme-at-io.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - NLP
    - Text matching
    - Question answering
    - Attention mechanism
---

2018-07-26晚上8点浩蓝受将门邀请分享他们在text matching最新的研究进展，浩蓝在将门nlp技术社群分享了这篇论文，所以在分享会之前先阅读下论文详情，带着问题参加分享。

### 场景
搜索直达
语义召回
相关性模型

短文本匹配
长文本匹配

### 背景

如果你不想阅读整篇文章，这里为你准备了一个总结：

### 前导知识与背景
短文本匹配有2大类解决方案，分别为:Presentation based Method和Interaction based Methods

###### Presentation based Methods

这类方法的思路都是用各种CNN、RNN等模型先去生成文本的特征表示(feature vector), 然后通过计算2个特征向量的相似度来度量文本的匹配度，这一类方法的区别就在于如何去计算文本的特征表示以及如何去度量特征向量的相似度。

###### Interaction based Methods

**基本假设** :两个句子的全局匹配度是由局部匹配度经过融合得到的;  
**主体思想** :先计算两个句子的局部匹配度，然后再去学习合适函数关系将局部匹配度融合起来作为最终的匹配度;  
**实现思路** :这类方法的思路是先去求文本对的匹配矩阵，然后利用各种模型从匹配矩阵中学习出有意义的特征，进行最终的相似度计算;  
**优点** :(相对于Presentation based Methods)
1. 更直接的表示局部匹配的关系
2. 保留了更多的结构信息(匹配的位置信息)；

**缺点** :
1. 将word或者n-gram word作为特征的基本单元，没有考虑到自然语言的其它特征:例如句子的句法信息；
2. 很难建模global matching和local matching的关系；
3. 对于文本多角度的匹配特征缺乏统一的融合框架，使得增加新的匹配特征简单便捷；

### Mix Model
**主体思路**
1.  
2.
3. 

###### Mix Model想要解决什么问题？
再来具体化以前Interaction based Methods的缺点:

1. **仅仅从word embedding特征去计算local matching是有明显缺陷的, 对于短语来说会有明显的信息损失**
(例如: all in与in all, work hard与hard work)
2. 假设局部匹配的特征重要度相同，实际情况并不是这样，例如:两个句子中的关键词匹配特征明显比两个句子中常用词的匹配度特征更重要；
3. 

**Mix model如何解决上述问题**
1. 对句子进行多粒度切分，多粒度交叉计算local matching，尽可能少的信息损失；  
2. 通过引入attention mechanism对局部匹配特征对于全局匹配度的重要性加以描述； 
3. 
###### Local Matching

###### Local Matching and global matching
核心思想:
* 将文本切分成不同粒度的sequence，通过卷积核学习文本不同粒度的表示， 根据文本表示计算相似度矩阵;
* attention机制: 利用idf attention、pos attention、 position attention分别对原始的相似度矩阵加权;
* 将上述多通道的相似度矩阵融合起来最为最终的特征;

### 疑问
1. 对文本切分成序列的详细处理方式；
2. 求文本卷积的处理方式，为什么论文图上看起来是二维矩阵;
3. 相似度矩阵是如何求出来的；
4. 如果对文本划分序列的话，相应的attention矩阵如何计算？
5. tri-gram特征爆炸如何处理? 通过hash function哈希到固定的bucket;
6. 向量空间如何实现快速索引?
7. 短文本匹配和长文本匹配有什么区别?

![Force Events](http://www.mobilexweb.com/wp-content/uploads/2015/09/foceevents.png)


相信你已经从它们的名字中意识到了，这些事件都是基于鼠标而非触摸的，毕竟它们是为 MacBook 设计的。并且，TouchEvent 也并没有包含 `webkitForce` 这个属性，它仅仅存在于 MouseEvent 里。在 iOS Safari 里，你确实可以找到 `onwebkitmouseforce` 这一系列事件处理器，但是很可惜它们并不会被触发，click 返回的 MouseEvent 也永远只能得到一个 `webkitForce: 0`


可喜可贺的是，故事还没有结束。[Touch Events v2 draft spec（触摸事件第二版草案）](https://w3c.github.io/touch-events/) 中正式添加了 `force` 属性。3D Touch 也得以在 iPhone 6s 与 6s+ 中通过 TouchEvent 访问到。不过，笔者也要在这里提醒大家，由于没有 `webkitmouseforcechange` 这样给力的事件，在手机上我们只能通过 **轮询 TouchEvent 的做法** 来不断检测压力值的改变……非常坑爹

[@Marcel Freinbichler](https://twitter.com/fr3ino) 第一个在 Twitter 上晒出了自己的 [Demo](http://freinbichler.me/apps/3dtouch)。在 6s 或 new Macbook 的 Safari（目前仅 Safari 支持）上访问就可以看到圆圈会随着压力放大。墙内的小伙伴可以直接试试下面这个圆圈，体验下 3D/Force Touch 带来的的奇妙体验。

<iframe src="//huangxuan.me/forcify/" style="
    width:100%;
    height:500px;
    border: 0;
"></iframe>

如果你不巧在用不支持 3D/Force Touch 的设备，发现尼玛用力按下去之后居然圆圈也有反映！？

放心，这真的不是你的设备突然习得了“感应压力”这项技能，而是因为 [Forcify](http://huangxuan.me/forcify) 是一个用于在所有设备上 polyfill 3D/Force Touch API 的 JS 库……它不但封装了 OSX/iOS 两个平台之间 API 的差异，还使用"长按"来模拟了 `force` 值的变化……



##### iPad Pro

全新的 iPad Pro（12.9 寸）打破了以往 iPad 渲染网站的方式。在此之前，市面上所有的 iPad（从初代 iPad，到 iPad Air 4，到 iPad Mini）都是以 768px 的宽度提供 viewport。

而屏幕更大的 iPad Pro 选择了宽 1024px 的 viewport，这使得它天生就能容纳更多的内容。不少人说iPad Pro 就是抄 Microsft Surface Pro 的嘛……嗯哼，IE/Edge 在 Surface Pro 上就是以 1024px 作为视口宽度的……

从交互的角度上来说，iPad Pro 虽然不支持 3D Touch，但是可以搭配 Smart Keyboard 与/或 Apple Pen（带有压力侦测）使用。对于键盘其实并没有什么好说的，如果一个网站在搭配键盘的桌面电脑上好用，它在 iPad Pro 上应该也不赖。而对于 Apple Pen，很可惜，目前似乎并没有 API 能让你在网站上获得这根笔的压力与角度。


### 新的 iOS 操作系统特性

##### iPad 上的多任务处理

自 iOS 9 起，iPad 允许两个应用在同一时刻并肩执行，有三种方式：**Slide Over**，**Split View** 与 **Picture-in-Picture**。不过，每一种方式都有其硬件需求，比如说 Slide Over 需要 iPad Air, iPad Mini 2 以上的设备，而 Split View 由于对内存的要求目前只支持 iPad Air 2 与 iPad Pro。

###### Slide Over（滑过来！）

Slide Over 支持的 App 并不多，不过 Safari 名列其中，这意味着我们的网站将可能在这个模式下被渲染。当网站处于 Slide Over 模式下时，它将在屏幕的右 1/4 位置渲染，并且置于其他 native app 之上。

这个模式也为 Responsive Web Design（响应式网站设计）提出了新的挑战：**一个只为 iPad 优化的网站，也需要能在该设备上以无需手动刷新的形式支持小屏幕的渲染。**因此，如果你正在使用服务器端探测（RESS），那么你的 iPad 版本需要以某种方式包含手机版本的网站，或者在进入该模式后重新加载一次。（如果你不了解 RESS，你可以观看我的[另一篇博文](/2014/11/20/responsive-web-design/)）


![Slide Over](http://www.mobilexweb.com/wp-content/uploads/2015/09/slideover.png)

在这个模式下，无论横屏还是竖屏，所有的 iPad（包括 Pro）都会把你的网站以 320px 的 viewport 宽度进行渲染，就好像在一个大 iPhone 5 上一样。你可以在 CSS 中通过 media query（媒体查询）探测到这个模式：

```css
/* iPad Air or iPad Mini */
(device-width: 768px) and (width: 320px)
/* iPad Pro */
(device-width: 1024px) and (width: 320px)
```

###### Split View（分屏视图）

在较新版本的 iPad 上，你可以将 Slide Over 的 Side View（侧视图）升级为 Split View。此时，两个应用将以相同比例在你的屏幕上同时工作。

在这个模式下，我们的网站将可能……

* **以屏幕 1/3 比例渲染时**，viewport 在 iPad Air/mini 犹如 iPhone 5，宽 320px。而在 iPad Pro 上则像是 iPhone 6：宽 375px
* **以屏幕 1/2 比例渲染时**，viewport 在 iPad Air/mini 上呈现为 507px 宽，而在 iPad Pro（横屏）下呈现为 678px 宽
* **以屏幕 2/3 比例渲染时**，viewport 在 iPad Air/mini 上呈现为 694px 宽，而在 iPad Pro（横屏）下呈现为 981px 宽

![Split View](http://www.mobilexweb.com/wp-content/uploads/2015/09/splitview.png)


###### Picture in Picture（画中画）

在一些较新版本的 iPad 上，使用 HTML5 video 标签的网站可以将其暴露到 Picture in Picture 机制中。通过 API（本文稍后会讲）或用户的触发，视频可以独立于网站在其他应用的上方继续播放。

![Picture in Picture](http://www.mobilexweb.com/wp-content/uploads/2015/09/pip.png)


##### iOS 9 下的响应式网页设计

下图向你展示了 iOS 9 所有可能的 viewport 尺寸，检查检查你的响应式断点都包含它们了吗？

![iOS 9RWD](http://www.mobilexweb.com/wp-content/uploads/2015/09/ios9rwd.png)

##### Safari View Controller 

如果你用过 Twitter 或者 Facebook（或者微信，微博……），那么你一定知道很多 native app 在打开一个网页链接时并不会默认使用 Safari。它们试图让你留在它们的应用里，所以通过提供 webview 让你在应用内进行网页浏览。可是问题在于，这类 webview 并不会与浏览器共享 cookies，sessions，autofill（自动填充）与 bookmark（书签），为了解决这些问题，就有了 Safari View Controller。

现在，native app 可以使用 Safari View Controller 来打开网站，它提供与 Safari 完全一致的隐私政策、local storage，cookies、sessions 同时让用户留在你的 app 中，它通过一个 “Done”（完成）按钮使用户可以回到 native app 的上一个 controller。这个全新的 controller 还可以让我们在 Share（分享）按钮上添加自定义的操作，这些操作在用户使用 Safari 应用时并不会出现。同时，native app 对这个自定义 Safari 实例具有完全的内容控制，你可以屏蔽不想被渲染的内容。

当你需要基于 web 的鉴权，比如 OAuth 时，使用 Safari View Controller 同样是一个好主意，这样就不再需要打开浏览器再重定向回你的应用。不过注意了，Safari View Controller 只适用于在线、公开的 web 内容。如果你的 web 内容假设在本地或者私服，那么 WKWebView 仍然是最推荐的选择。

> 笔者八卦一下，Safari View Controller 实际上也算是半个社区推进的产物。早在 2014 年 12 月，Tumblr 的 iOS 工程师 Bryan 就发表了一篇著名的 [We need a “Safari view controller”](http://bryan.io/post/104845880796/we-need-a-safari-view-controller) 叙述现有 webview 在第三方登录鉴权时的窘境。
> 2015 年 6 月，Apple Safari 工程师 Ricky Mondello 的 Twitter 宣告了这个设想的落地：You all asked for it. Come see me introduce it. Introducing Safari View Controller 1:30 PM, Tuesday. Nob Hill.


##### Safari Content Blockers

现在，iOS 9 上的 Safari 支持一种全新的 App Extensions（应用拓展）：**Content Blocker**（内容拦截器）。这类拓展以 native app 的形式存在，你可以在 App Store 上下载到，它们可以拦截 Safari 内的任何内容，包括：跟踪器、广告、自定义字体、大图片、JavaScript 文件等等。

作为 web 开发者，尽管我们不能禁用 Content Blocker，我们仍然应该注意到它们的存在。诸如 Crystal 的一些拦截器宣称他们[可以提高网页的打开速度](http://murphyapps.co/blog/2015/8/22/crystal-benchmarks)。Crystal 声称可以加快网页的加载速度 3.9 倍并且少用 53% 的带宽。不过问题是：到底哪些东西被拦截器拦截了？[这篇文章](http://thenextweb.com/apple/2015/08/27/content-blocking-in-ios-9-is-going-to-screw-up-way-more-than-just-ads/)提到了一些我们未来可能会遇到的问题。

![crystal](http://www.mobilexweb.com/wp-content/uploads/2015/09/crystal.png)

在 iOS 9 发布后，Peace，一个 Content Blocker，曾在 App Store 排名跻身前十。从用户的角度来说，如果一个网站由于被 Content Blocker 拦截了某些重要资源而不能正常工作，你可以长按重新加载按钮并且以不启用 Content Blocker 的方式重新加载这个网站（见下图，来自 MacWorld.com）

![disable content blocker](http://www.mobilexweb.com/wp-content/uploads/2015/09/macworld.png)

Content Blocker 能隐藏元素，也有能力通过 CSS 选择器、域名、类型、或者 URL 来过滤并拦截某个文件的加载，[Purify Blocker](https://itunes.apple.com/us/app/purify-blocker-fast-clutter/id1030156203?ls=1&mt=8) 给用户提供了拦截某一种内容类型的进阶选项，比如 Web Fonts。

![purify](http://www.mobilexweb.com/wp-content/uploads/2015/09/purify.png)

##### WKWebView 的增强

UIWebView 已经被官方弃用，虽然它还在在那，不过它再也不会得到什么升级。与此相反，WKWebView 正在取代它的位置。一个最受期待的特性现在终于推出：加载本地文件到 WKWebView。因此，现在 Apache Cordova 应用与其他 web 内容都可以直接从 iOS 包中使用本地文件了，不再需要各种诡异的 hack 了。

此外，还有一些新特性也一并推出。比如说，通过 WKWebsiteDataStore，Objective-C 或 Swift 有能力查询与管理 webview 的本地存储（比如 localStorage 或 IndexedDB）。这就允许我们将原有的数据存储替换成新的某些东西，比如说替换成一个不永久的（Chrome for iOS 的隐身模式就需要这种东西）

##### Universal Links（通用链接）

如果你既有一个网站，又有一个 native app，你现在可以通过 Universal Links 来增强用户体验。它允许你在操作系统内“占有”自己的域名，这样，一切指向你网站的链接都会被重定向到你的 app。

目前，所有的 app 都是通过自定义 URI 来达到这个效果的，比如 `comgooglemaps://` 就可以用来从网站或者其他原生 iOS 应用中打开 Google Maps。

想要提供这个特性的话，你首先需要在 native app 中实现 Deep Linking（深度链接），让应用中的内容与 Safari 的 URL 吻合。然后，你需要在 Apple 的网站上关联你的域名，取得这个域名的 SSL 认证并且把签名后的 JSON 部署到该域名上。这是为了防止第三方的应用“占据”了属于你而不属于他们的域名，比如说 twitter.com 被非 Twitter 的其他应用占据掉。

目前唯一的缺点是用户好像并不能决定到底以哪种方式来打开内容（使用 web 还是 app），不过我们可以观望一段时间看看它会如何发展。在不远的这段时间里，你可能会发现在网站或 Google 搜索里点击一个链接时会没有任何预警的就跳进了 native app 里。

##### App Search（应用搜索）

Apple 带着自己的 web 蜘蛛杀进了搜索的市场，而我们需要支持它得以在 Siri 与 Spotlight 中提升自己的曝光率。这在我们同时拥有网站与 app 时尤为重要，因为现在 Apple 会索引你网站的内容，但打开时却可能将用户带到了 app 里去。

尽管这会开启 SEO 的新篇章，不过却相当容易。你需要使用一些标签标准，诸如 [Web Schema](http://schema.org/)、[AppLinks](http://applinks.org)、[OpenGraph](http://ogp.me) 或者 [Twitter Cards](https://dev.twitter.com/cards/mobile)，配合上 App Banner 与 `app-argument`，如果你有你自己的 native app 的话。

> 关于“让你的网页支持 Apple 搜索”的更多详情，请查阅 Apple 官方文档 [Mark Up Web Content](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/WebContent.html#//apple_ref/doc/uid/TP40016308-CH8-SW5)

Apple 刚刚发布了一个 [App Search Validation Tool（应用搜索验证工具）](https://search.developer.apple.com/appsearch-validation-tool/)来帮助你搞清楚，需要向你的网站添加什么才能支持 App Search

![App Search](http://www.mobilexweb.com/wp-content/uploads/2015/09/appsearch-1024x467.png)

##### CloudKit JS

如果你拥有一个 native app，你很可能会将用户数据保存在 iCloud 上。在过去，只有 iOS 与 Mac 应用被允许使用它。现在，通过 CloudKit JS，你的网站也可以连接上 iCloud 数据了。

##### Back Button

现在，当你链接到一个 native app 时（通过自定义 URI 或者 Universal Link），Safari 会询问用户是否想要使用 native app 打开这个链接（见下图）。如果用户同意了，这个应用将被打开，并且在左上角会有一个返回按钮可以返回 Safari ，返回到你的网站。

<img src="http://www.mobilexweb.com/wp-content/uploads/2015/09/back.png" alt="backbutton" width="320" />

### 新的 API 支持

##### Navigation Timing API

Navigation Timing API 在 iOS 9 迎来了回归。让我们回忆一下，这货添加于 8.0 却在一周后的 8.1 中去掉了。这对于 Web 性能是个好消息。通过这个 API，我们可以更精确的测量时间，还可以获得一系列有关加载过程的时间戳，它们对于追踪与在真实场景中做决策来改进用户体验都非常有用。


##### Picture in Picture

PiP API（被称为 Presentation Mode API）目前只支持 iOS，它允许我们手动让一个 `<video>` 元素进入或离开 PiP 模式如果 `video.webkitSupportsPresentationMode` 是支持的。

举个例子，我们可以在内嵌模式与 PiP 模式中切换：

```js
video.webkitSetPresentationMode(
    video.webkitPresentationMode === "picture-in-picture" ?
    "inline" : 
    "picture-in-picture"
);
```

我们还可以通过新的 `onwebkitpresentationmodechanged` 事件来检测 Presentation Mode（展示模式）的变化。


##### Backdrop CSS

iOS 7 与最近的 Mac OS 使用 Backdrop filter（背景滤镜）来模糊背景（指 native 开发），而在网站上实现这个却并不容易。

iOS 9 上的 Safari 现在支持了来自 Filter Effect v2 spec（滤镜特效第二版规范）的 **backdrop-filter**。比如说，我们可以使用一个半透明的背景并且对其背后的背景使用滤镜：

```css
header {
   background-color: rgba(255, 255, 255, 0.4);
   -webkit-backdrop-filter: blur(5px);
   backdrop-filter: blur(5px);
}
```

![backdrop](http://www.mobilexweb.com/wp-content/uploads/2015/09/backdrop.png)


##### CSS Scroll Snapping

在 web 上实现分页内容（比如相册跑马灯）总是非常麻烦，无论是使用 JavaScript 框架、touch 事件还是 hacking 滚动条等等。Apple 新添加了一个很赞的 CSS 特性叫做 CSS Scroll Snapping。这个特性新增了一系列的 CSS 属性让你定义规则或者不规则的 snap zone（停留区域），这样滚动的位置就会“啪”地一下停在这个区域，而非像以前一样可以停在任何地方。  

来看个例子：

```css
#photo-gallery{
    width: 100%;
    overflow-x: scroll;
    -webkit-scroll-snap-points-x: repeat(100%);
    -webkit-scroll-snap-type: mandatory;
}
```

> 想要看个跑起来后的例子？笔者为大家准备了 webkit 的官方 [demo](http://www.webkit.org/demos/scroll-snap/)，不过这个属性目前只支持 iOS 9 Safari 哦，并不支持 webview


##### CSS Supports

CSS Supports，包括 CSS `@supports` 与来自 CSS Conditional Rules Module Level 3 spec 的 JavaScript CSS Supports API 都在 iOS 上迎来降临。现在，我们可以针对某个 CSS 属性的特定值的支持情况来编写代码：

```css
@supports(-webkit-scroll-snap-type: mandatory) {
    /* we use it */
}
```

同样，使用 JavaScript：

```js
if (CSS.supports("-webkit-scroll-snap-type", "mandatory")) {}
```

##### 一些细微的改进

* ECMAScript 6 的更完善支持：classed、computed properties、template literial 与 week sets
* 新的 CSS Level4 伪类/元素选择器：`:not`、`:matches`、`:any-link`、`:placeholder-shown`、`:read-write`、`:read-only`
* Native app 现在可以通过 extension 来向 Safari 的 Shared Links（分享链接）窗口上注入信息
* 大量无前缀 CSS 属性的支持（终于），比如 transition、animation、@keyframes、flex 与 columns
* Mac OS El Capitán 上的 Safari 9 提供了一个全新设计的 Web Inspector（Web 检查器）。幸运的是，iOS 9 的远程调试完全兼容 Mac OS 上的 Safari 8，所以你倒是不用急着升级你的 Mac OS
* iOS 9 通过 `-apple-font` 加入了一些 Dynamic Fonts（动态字体），并且它们现在应用的是 Apple 的新字体：San Francisco，笔者的博客就已经用上它啦
* scrollingElement 现在可用了
* `<input type=file>` 现在允许你从 iCloud Drive 与已安装的第三方应用，比如 Google Drive 中选择文件

<img src="http://www.mobilexweb.com/wp-content/uploads/2015/09/IMG_2017.png" alt="input file" width="320" />

* 当你加载一个 HTTPS 协议的页面时，你不能混用 HTTP 与 HTTPS 的资源

### 著作权声明

本文译自 [iOS 9, Safari and the Web: 3D Touch, new Responsive Web Design, Native integration and HTML5 APIs --- Breaking the Mobile Web](http://www.mobilexweb.com/blog/ios9-safari-for-web-developers)   
译者 [黄玄](http://weibo.com/huxpro)，首次发布于 [Hux Blog](http://huangxuan.me)，转载请保留以上链接
