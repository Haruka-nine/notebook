
# 安装
如果要使用tensorRT，那就不能全部使用虚拟环境了

因为tensorRT在windows下是不支持使用pip安装的

我们必须在本机环境下配置CUDA,cuDNN

## CUDA安装
非常迅速，到CUDA官网找到安装包下到桌面以后直接安装即可  
[直转CUDA官网](https://developer.nvidia.com/cuda-toolkit-archive)  
下载至桌面后直接点击exe文件安装即可  
安装以后我的电脑会自动配置CUDA的环境变量，因此不再需要自己配一遍

## cuDNN安装
也是非常直接，去到cuDNN官网  
[直转cuDNN官网](https://developer.nvidia.com/cudnn)  
直接下载对应版本即可  
下载后把bin、lib、include里的文件复制到CUDA安装路径下即可  
复制完以后不需要额外配置cuDNN的环境变量

## pyCUDA安装
pycuda并不是tensorRT的安装必备项，属于可选项，安装方法使用whl文件手动安装，去官网下载安装包即可  
[直转pyCUDA下载链接](https://www.lfd.uci.edu/~gohlke/pythonlibs/#pycuda)  
在控制台使用命令  
`pip install 'pyCUDA解压路径'`  
安装即可

## tensorRT安装
https://developer.nvidia.com/nvidia-tensorrt-8x-download
tensorRT安装比较复杂，其需要前述预安装套件的版本组合，以tensorRT8.2.5为例，其仅支持如下几个CUDA版本

![[Pasted image 20230405143702.png]]

具体而言，以自身需要的tensorRT版本为出发点，可以通过查阅tensorRT官网的文档进行选择
tensorRT安装配置查询
安装步骤：
1、去tensorRT官网下载安装包
tensorRT下载官网
2、解压缩
3、将tensorRT解压缩路径下lib文件夹里的DLL文件复制到CUDA文件夹的bin目录下
4、在已安装好tensorflow-gpu的conda环境下安装tensorRT，安装包在解压的tensorRT文件夹的python目录下，根据环境中的python版本选择安装包
`pip install tensorrt-*-cp3x-none-win_amd64.whl`
5、（这一步不是必要的）安装后续配置包，主要是uff, graphsurgeon, onnx_graphsurgeon三个包，在tensorRT的解压缩文件夹里都可以找到，直接pip install即可
更为细节的，可以查看官网安装指南
tensorRT官方安装指南



# 推理加速

`python export.py --weights yolov5s.pt --include engine --device 0 `

使用上方命令就可以对yolov5模型进行导出

```ad-info
注意一个问题，tensorRT对图像进行推理时，有一个要求，尺寸是固定的。
这个尺寸是在上方命令导出时就需要规定好的，默认时640×640
也就是说，使用上方导出后的模型，即使我们对340×640的模型进行推理，也会转化成640×640进行推理
理所当然，这增加了消耗，因此，如果我们使用tensorRT对图像进行推理，需要在导出时根据图像的尺寸设置对应的参数
```
