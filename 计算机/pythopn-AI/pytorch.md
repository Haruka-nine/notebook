
# 安装

在本机上安装cuda和cudnn,这部分参考[[tensorRT#CUDA安装]]    [[tensorRT#cuDNN安装]]

pytorch官网：https://pytorch.org/get-started/locally/

依照本机上安装的cuda版本选择对应版本，**使用pip安装**
![[Pasted image 20230418162226.png]]

```ad-attention
title:cuda问题

关于cuda是否需要本机安装

1.其实可以不在本机安装，使用上方的命令，在虚拟环境中也会给我们安装好cuda,我们直接就可以使用

2.但如果需要编译、部署，那我们还是需要本机安装cuda的。

3.**建议本机和虚拟环境的cuda版本一致，防止出现问题**

```



例如，写这篇时，我本机是11.7的cuda,我就使用上方的语句
```cmd
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu117
```


验证安装是否成功代码：
```python
import torch  
import torchvision  
print(torch.cuda.is_available())
```


# torchhub模型预测

## pt模型

`model = torch.hub.load("./","custom",path="yolov5s.pt",source="local")`


四个参数，
- 第一个是yolov5的根目录（其实就是hubconf所在的文件夹，因为要去这个路径读hubconf）
- 第二个如果是自定义的模型，一定一定要写 custom,如果是官方模型，可以写模型名称
- 第三个，如果是自定义模型就写模型地址
- 第四个，代表这个是本地模型,从本地找，默认是github,回去官方的github上下载

```python
imgs = [
		'data/images/11.jpg',   #filename
		Path('data/images/11.jpg'), #Path
		'https://ultralytics.com/imges/1.jpg',# URL
		cv2.imread("data/bus.jpg"), # openCV
		Images.open("data/bus.jpg"), #PIL
		np.zeros((320,640,3))#numpy
]

# 支持上述多种格式的预测，带来很多方便
results = model(img)
```


---

results的相关函数

`results.show()`   显示图像（这里只有显示时有框，results并没有改变）

![[Pasted image 20230424165650.png]]

```ad-info
注意，一旦经过render,results就会被改变，这个results的图像上有存在框了
```

`results.render()`  将图像绘制框，返回一个list

![[Pasted image 20230424165749.png]]

`results.pandas().xyxy[0]`    pandas的DataFarm形式，分别为每个目标的框的xy最大最小坐标，概率，类别，类别名

![[Pasted image 20230424165855.png]]

`results.pandas().xyxyn[0]`   pandas的DataFarm形式,和上方一样，但xy变为归一化

![[Pasted image 20230424170039.png]]

`results.pandas().xywh[0]`  pandas的DataFarm形式,x中心点，y中心点，框长，框高

![[Pasted image 20230424170136.png]]


`results.crop(save=False)`   对每个框的属性进行汇总，形成一个list，list的im是根据框提取出来的分割图片数组
![[Pasted image 20230424171234.png]]

![[Pasted image 20230424171247.png]]

我们可以像上边那样，读取list的第三个，也就是人，然后获取im的信息，然后显示出来


`str(results)`  使用这个就可以得到一段话，是对识别结果的简单总结

![[Pasted image 20230424171655.png]]


## tensorRT的问题

`model = torch.hub.load("./","custom",path="yolov5s.engine",source="local")`


---
问题一：
我们直接导入这个的话，是会报错的。
还是那个原因，tensorRT需要尺寸相同。
使用detect进行预测时，detect会自动将尺寸更改为tensorRT的尺寸
但torchhub不会，所以如果使用640×640的tensorRT模型去预测340×640的是会报错的
需要更改图片尺寸或者更改模型适配尺寸

问题二：
tensorRT模型尽心预测，画出来的框是不会标记person,tie这样的name的，而是class1,class2
因为yolov5并没有对tensorRT的modle进行names的设定
所以我们需要自己设定
![[Pasted image 20230424173633.png]]

设定完成就没有问题了

