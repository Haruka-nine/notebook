# 简介

## React是什么？
用于构建用户界面的JavaScript库
中文官网：https://react.docschina.org/

前端页面显示流程：
1. 发送请求获取数据
2. 处理数据（过滤、整理格式等）
3. **操作DOM呈现页面**
React做的就是第三步，对前两步，并不提供帮助

```ad-info
title:从新定义
React是一个将数据渲染为HTML视图的开源JavaScript库
```

## 为什么要使用React?

1. 原生JavaScript操作DOM繁琐、效率低（DOM-API操作UI）
2. 使用JavaScript直接操作DOM,浏览器会进行大量的重绘重排
3. 原生JavaScript没有组件化编码方案，代码复用率低

## React的特点

1. 采用**组件化**模式、**声明式编码**，提高开发效率和组件复用率
2. 在React Native中可以使用React语法进行**移动端开发**
3. 使用**虚拟DOM**+优秀的**Diffing算法**，尽量减少与真实DOM的交互
