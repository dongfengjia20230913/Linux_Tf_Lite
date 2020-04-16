上接：

Linux手上构建Tensorflow lite

上一边文章，我们构建了最简单的minimal的例子，本章节，我们编译下label_image的例子

为了编译label_image的例子，我们修改了buid和Makefile文件，是的可以指定编译任何的实例；
修改后的文件为
[build文件内容](https://github.com/jdf-eng/Linux_Tf_Lite/blob/master/build_common.sh)

[MakeFile文件内容](https://github.com/jdf-eng/Linux_Tf_Lite/blob/master/MakeCommonfile)


## 编译 ##

label_image的build_common.sh文件为

```
set -x
set -e # if any express return fale, exit!


SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
TENSORFLOW_DIR="${SCRIPT_DIR}/../../../.."

FREE_MEM="$(free -m | awk '/^Mem/ {print $2}')"
# Use "-j 4" only memory is larger than 2GB
if [[ "FREE_MEM" -gt "2000" ]]; then
  NO_JOB=4
else
  NO_JOB=1
fi

#code enter fiter

make -j ${NO_JOB}  -C "${TENSORFLOW_DIR}" -f tensorflow/lite/tools/make/MakeCommonfile TARGET_PROGRAM_SRCS="\ tensorflow/lite/examples/label_image/label_image.cc	\tensorflow/lite/examples/label_image/bitmap_helpers.cc"

```

编译成功后，会在响应目录生成可执行文件

```
~tensorflow/lite/tools/make/gen/linux_x86_64/bin$ ls
benchmark_model  benchmark_model_performance_options  tf_common

```

## 应用图像分类 ##

下载[模型和标签文件](https://storage.googleapis.com/download.tensorflow.org/models/tflite/mobilenet_v1_1.0_224_quant_and_labels.zip);并获取TF源码中的示例图片tensorflow/lite/examples/label_image/testdata/grace_hopper.bmp

运行命令：
```
tensorflow/lite/tools/make/gen/linux_x86_64/bin$ ./tf_common  -v 1 -m ./mobilenet_v1_1.0_224_quant.tflite -i ./grace_hopper.bmp -l ./labels_mobilenet_quant_v1_224.txt 

```


图片加测结果：

```
average time: 407.108 ms 
0.780392: 653 military uniform #%78概率是军装
0.105882: 907 Windsor tie %10的概率是绳子
0.0156863: 458 bow tie #领带
0.0117647: 466 bulletproof vest #防弹衣
0.00784314: 835 suit #西装
```


## 报错分析 ##

```
label_image.cc:(.text+0x39): undefined reference to `tflite::evaluation::CreateGPUDelegate()'
/home/jiadongfeng/tensorflow/tensorflow-r2.2/tensorflow/lite/tools/make/gen/linux_x86_64/obj/tensorflow/lite/examples/label_image/label_image.o: In function `tflite::label_image::GetDelegates[abi:cxx11](tflite::label_image::Settings*)':
label_image.cc:(.text+0xa2c): undefined reference to `tflite::evaluation::CreateHexagonDelegate(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, bool)'
label_image.cc:(.text+0xb06): undefined reference to `tflite::evaluation::CreateNNAPIDelegate()'
```

修改方法：
因为我的电脑不支持GPU加速，Intel的。将相关加速代码去除即可