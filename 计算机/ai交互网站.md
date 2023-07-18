
# 架构简图
![[Pasted image 20230718212714.png]]

# ai服务

## Stable Diffusion

- ui服务和api服务提供 ：https://github.com/AUTOMATIC1111/stable-diffusion-webui
- 绘画模型理论：https://lilianweng.github.io/posts/2021-07-11-diffusion-models/

## ai语音

- 服务提供：https://github.com/jaywalnut310/vits
- vits模型理论：https://arxiv.org/abs/2106.06103

对于情感模块的添加参考vtc，或者加入情感模块项目

## chatGpt
- 可以直接使用chatgpt的url，付费使用
- 可以更新显卡到8g以上，自己部署chatgml，对模型进行调试（等发工资）

# 客户端

选用VUE或者React进行客户端的编写。
时间不够就使用Vue（方便编写小程序）
时间充足可以选用React(方便编写安卓app)

选用vue可以移植之前做的管理系统，前台移植ChatGpt对话项目

# 服务端

选用springboot，如果加入很多服务，可以考虑springcloud分部式，前提是有多台电脑



