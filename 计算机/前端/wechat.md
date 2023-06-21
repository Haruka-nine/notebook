# 小程序相关

# wxml 语法

###  1.  数据绑定

小程序的数据是写在Page中的data中的，但小程序中没有做数据劫持，所以数据没有在this上直接获得，需要this.data.key获取数据

1. 小程序
   1. data中初始化数据
   2. 修改数据： this.setData()
      1. 修改数据的行为始终是同步的
   3. 数据流： 
      1. 单项： Model ---> View
2. Vue
   1. data中初始化数据
   2. 修改数据: this.key = value
   3. 数据流： 
      1. Vue是单项数据流： Model ---> View
      2. Vue中实现了双向数据绑定： v-model
3. React
   1. state中初始化状态数据
   2. 修改数据: this.setState()
      1. 自身钩子函数中(componentDidMount)异步的
      2. 非自身的钩子函数中(定时器的回调)同步的
   3. 数据流： 
      1. 单项： Model ---> View

### 2.事件绑定

Vue中事件需要写在method中，而小程序中事件和data还有钩子函数是平级的，写在一起

bind绑定：事件绑定不会阻止冒泡事件向上冒泡

```html
  <view class="goStudy" bindtap="handleParent">
    <text bindtap="handleChild">hello world</text>
  </view>
```

catch绑定：事件绑定可以阻止冒泡事件向上冒泡

```html
  <view class="goStudy" catchtap="handleParent">
    <text catchtap="handleChild">hello world</text>
  </view>
```

### 3.路由跳转

在小程序中路由跳转并不是通过语法而是通过api来跳转

api详情看官网的api路由页面

https://developers.weixin.qq.com/miniprogram/dev/api/route/wx.switchTab.html

路由跳转的界面的窗口选项可以直接写在局部的json中，不需要加window，直接写

### 4. 生命周期
![[Pasted image 20221014154734.png]]

onLoad和onReady可以发请求

onLoad没有data数据，只有样式

onShow可以执行多次

## 2. 获取用户基本信息

22 .10.25以后无法获取用户昵称和头像，提供用户填写昵称和头像选项的接口

自 2022 年 10 月 25 日 24 时后（以下统称 “生效期” ），用户头像昵称获取规则将进行如下调整：

1. **自生效期起，小程序 wx.getUserProfile 接口将被收回**：生效期后发布的小程序新版本，通过 wx.getUserProfile 接口获取用户头像将统一返回默认[灰色头像](https://mmbiz.qpic.cn/mmbiz/icTdbqWNOwNRna42FI242Lcia07jQodd2FJGIYQfG0LAJGFxM4FbnQP6yfMxBgJ0F3YRqJCJ1aPAK2dQagdusBZg/0)，昵称将统一返回 “微信用户”。生效期前发布的小程序版本不受影响，但如果要进行版本更新则需要进行适配。
3. **自生效期起，插件通过 wx.getUserInfo 接口获取用户昵称头像将被收回**：生效期后发布的插件新版本，通过 wx.getUserInfo 接口获取用户头像将统一返回默认[灰色头像](https://mmbiz.qpic.cn/mmbiz/icTdbqWNOwNRna42FI242Lcia07jQodd2FJGIYQfG0LAJGFxM4FbnQP6yfMxBgJ0F3YRqJCJ1aPAK2dQagdusBZg/0)，昵称将统一返回 “微信用户”。生效期前发布的插件版本不受影响，但如果要进行版本更新则需要进行适配。通过 wx.login 与 wx.getUserInfo 接口获取 openId、unionId 能力不受影响。
4. **「头像昵称填写能力」支持获取用户头像昵称**：如业务需获取用户头像昵称，可以使用「[头像昵称填写能力](https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/userProfile.html)」（基础库 2.21.2 版本开始支持），具体实践可见下方《最佳实践》。
5. **小程序 wx.getUserProfile 与插件 wx.getUserInfo 接口兼容基础库 2.21.2 以下版本的头像昵称获取需求**：上述「头像昵称填写能力」从基础库 2.21.2 版本开始支持（覆盖微信 8.0.16 以上版本）。对于来自更低版本的基础库与微信客户端的访问，小程序通过 wx.getUserProfile 接口将正常返回用户头像昵称，插件通过 wx.getUserInfo 接口将返回用户头像昵称，开发者可继续使用以上能力做向下兼容。

## 3. 前后端交互

1. 语法: wx.request()
2. 注意点: 
   1. 协议必须是https协议
   2. 一个接口最多配置20个域名
   3. 并发限制上限是10个
   4. **开发过程中设置不校验合法域名**： 开发工具 ---> 右上角详情 ----> 本地设置 ---> 不校验

## 4. 本地存储

1. 语法: wx.setStorage() || wx.setStorageSync() || .....
2. 注意点： 
   1. 建议存储的数据为json数据
   2. 单个 key 允许存储的最大数据长度为 1MB，所有数据存储上限为 10MB
   3. 属于永久存储，同H5的localStorage一样

# 扩展内容

## 1. 事件流的三个阶段

1. 捕获: 从外向内
2. 执行目标阶段
3. 冒泡: 从内向外

## 2. 事件委托

1. 什么是事件委托
   1. 将子元素的事件委托(绑定)给父元素
2. 事件委托的好处
   1. 减少绑定的次数
   2. 后期新添加的元素也可以享用之前委托的事件
3. 事件委托的原理
   1. 冒泡
4. 触发事件的是谁
   1. 子元素
5. 如何找到触发事件的对象
   1. event.target
6. currentTarget VS target
   1. currentTarget要求绑定事件的元素一定是触发事件的元素
   2. target绑定事件的元素不一定是触发事件的元素

## 3. 定义事件相关

1. 分类
   1. 标准DOM事件
   2. 自定义事件
2. 标准DOM事件
   1. 举例： click，input。。。
   2. 事件名固定的，事件由浏览器触发
3. 自定义事件
   1. 绑定事件
      1. 事件名
      2. 事件的回调
      3. 订阅方: PubSub.subscribe(事件名，事件的回调)
      4. 订阅方式接受数据的一方
   2. 触发事件
      1. 事件名
      2. 提供事件参数对象， 等同于原生事件的event对象
      3. 发布方: PubSub.publish(事件名，提供的数据)
      4. 发布方是提供数据的一方































