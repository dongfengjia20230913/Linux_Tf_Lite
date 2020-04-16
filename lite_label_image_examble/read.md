## 安装交叉编译包 ##

交叉编译是在一个平台上生成另一个平台上的可执行代码

要使用 TensorFlow Lite 交叉编译功能，应先安装工具链和相关的库。

```
sudo apt-get update
#安装后占用空间大小 15.4kb
sudo apt-get install crossbuild-essential-armhf
```

## 应用图像分类 ##

下载[模型和标签文件](https://storage.googleapis.com/download.tensorflow.org/models/tflite/mobilenet_v1_1.0_224_quant_and_labels.zip);并获取TF源码中的示例图片tensorflow/lite/examples/label_image/testdata/grace_hopper.bmp

运行命令：
```
tensorflow/lite/tools/make/gen/linux_x86_64/bin$ ./tf_common  -v 1 -m ./mobilenet_v1_1.0_224_quant.tflite -i ./grace_hopper.bmp -l ./labels_mobilenet_quant_v1_224.txt 

```


openwrt编译提示version `GLIBC_2.27' not found错误


