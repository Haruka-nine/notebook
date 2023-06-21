
# 安装

首先需要先安装[[pytorch#安装]]

github地址 :https://github.com/ultralytics/yolov5

创建好pytorch环境后，使用github拉取，或者下载下来后直接黏贴进虚拟环境中

然后安装requirements中的库就行，建议numpy改为1.20.3，默认的最高版本可能有冲突

# 模型检测

使用yolov5的detect.py

```python
python detect.py --source [写预测文件路径]
```

还有各种参数比如将预测显示为窗口等，详细看参数表

- 关键参数
	- weights:训练好的模型文件
	- source:检测的目标，可以是单张图片、文件夹、屏幕、或者摄像头等等
	- conf-thres: 置信度阈值，越低框越多，越高框越少
	- iou-thres:IOU阈值，越低框越少，越高框越多


- 基于torch.hub的检测方法
```python

# 示例代码
import torch  
   
# model ,参数代表的意思分别为，yolov5根目录，custom表示自定义模型，相对路径，本地资源  
model = torch.hub.load("../","custom",path="./weight/best.pt", source="local")
  
#Images  
img = "./data/images/zidane.jpg"  
  
#Inference  
results = model(img)  
  
#results  
results.show()
```


# 数据集构建

- 准备工作
	- 数据收集
		- 图片类型数据
		- 视频类型数据
			- 使用opencv进行视频抽帧
	- 标注工具
		- labelimg
			- 安装命令：pip install labelimg


下面代码将视频每10帧转化为图片
```python
video = cv2.VideoCapture("./sss.mp4")  
num = 0  #计数器  
save_step = 10 #间隔帧  
while True:  
    ret, frame = video.read()  
    if not ret:  
        break  
    num +=1  
    if num % save_step == 0:  
        print(1)  
        cv2.imwrite("./images/"+str(num)+'.jpg',frame)
```


```ad-note
title:labelimg基本使用
在命令行直接输入labelimg就可以启动
open Dir选择要要标记的图片文件夹
change save Dir 选择保存的label文件夹
将PastalVOC点成yolo，带年纪保存
打开Edit的自动保存
快捷键：w创建方框，a上一个，d下一个
```


或者使用roboflow
https://universe.roboflow.com/
设计自己的网络数据集库，并进行网上标注，然后导出为yolov5格式


# 模型训练

修改对应配置yaml文件，然后将train文件的参数修改为 对应的yaml文件，然后启动train进行训练就行。


![[Pasted image 20230418203808.png]]

也可以使用谷歌提供的网上GPU进行训练
https://colab.research.google.com/drive/1JBVZO2P1UXztdaW2LyD6BdHPYQch_DLY


# 模型部署

## 本地窗口框

使用pyside6
https://doc.qt.io/qtforpython-6/index.html

## web部署

gradio
https://gradio.app/docs/

## flask部署

查看yolov2的util文件夹的示例代码




# AutoDL服务器训练

https://www.autodl.com/market/list

在网站租用服务器，选择pytorch镜像

有问题看视频
https://www.bilibili.com/video/BV13s4y1V7b4/?spm_id_from=333.788&vd_source=d94f7d87c8b34cf8cf01cc38507b729b

将yolov5-master的压缩包上传到服务器，然后使用命令 unzip yolov5-master进行解压

然后进行conda的初始化 conda init

关闭终端，打开新终端就会进入conda环境

然后cd 进入yolov5的文件夹，安装依赖
`pip insatall -r requirment.txt`

这时基本安装会报错，根据提示修改那个对应的依赖的版本就行


---

pycharm与AutoDL进行联动使用（本地写代码，服务器训练）

首先，pycharm在设置中的SSH Configurations中创建一个服务器连接。

![[Pasted image 20230420214239.png]]


在python的环境依赖中添加 ssh Interpreter，这里就选取我们刚刚设置好的服务器

![[Pasted image 20230420214717.png]]


在终端中输入命令 whereis python，找到终端的python路径，我们需要conda的python环境
一般为/root/miniconda3/bin/python

Sync folders就是我们选择同步的文件夹（也就是本地文件上传到哪里）
![[Pasted image 20230420215030.png]]


然后切换Interpreter到这个新环境就可以，之后执行代码就都是在服务器上运行的



可以通过这个查看服务器文件情况，并下载服务器上跑出来的文件
![[Pasted image 20230420215711.png]]


![[Pasted image 20230420215830.png]]
可以在这里单独设置某些程序的环境




