# 在 Tensorflow2 上使用 yolov3 进行目标检测

该项目是 [AaronJny/tf2-keras-yolo3](https://github.com/AaronJny/tf2-keras-yolo3) 的分支，点击[此处](./README_old.md)查看原项目的自述文件。由于开发环境有所不同，因此我需要修改某些部分以正常运行代码，该项目可以在下述先决条件所给出的环境中运行。

### 先决条件

注意：使用 `pip show [模块名]` 指令可以查看模块的版本或检查是否存在；使用 `pip list` 指令可以查看安装的所有 Python 模块。

  * TensorFlow 版本：TensorFlow-GPU 2.7
  * keras 版本：2.7
  * protobuf 版本：4.22.1

## 运行预训练权重文件

在克隆仓库后，需要先下载预训练权重文件 `yolov3.weights`。、

运行代码：
```
python convert.py yolov3.cfg yolov3.weights model_data/yolo.h5
```
将在相应目录看到这一文件。此操作将 yolov3 预训练的权重文件转换为 keras 格式的权重文件。

如果 `protobuf` 版本过高，提示：
```
TypeError: Descriptors cannot not be created directly.
If this call came from a _pb2.py file, your generated code is out of date and must be regenerated with protoc >= 3.19.0.
```

因此，按照提示降级到 `3.19.6` 版本，这是因为：
```
ERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
tensorboard 2.12.0 requires protobuf>=3.19.6, but you have protobuf 3.19.0 which is incompatible.
```

<br>为兼容该仓库，`keras` 版本请安装为 `2.7`。

`yolo_video.py` 是执行推理的文件，其用法是：
```
usage: yolo_video.py [-h] [--model MODEL] [--anchors ANCHORS]
                     [--classes CLASSES] [--gpu_num GPU_NUM] [--image]
                     [--input] [--output]

位置参数:
  --input        视频输入路径（相对）
  --output       视频输出路径（相对）

optional arguments:
  -h, --help         显示当前的帮助信息并退出
  --model MODEL      模型权重文件的路径, 默认为 model_data/yolo.h5
  --anchors ANCHORS  anchor 定义的路径, 默认为
                     model_data/yolo_anchors.txt
  --classes CLASSES  类定义的路径, 默认为
                     model_data/coco_classes.txt
  --gpu_num GPU_NUM  GPU 的使用数量, 默认为 1
  --image            照片检测模式, 这将忽略所有位置参数
```


### 训练

**请注意：请仔细阅读 `settings.py` 的内容。**

用于训练的注释文件 `train.txt` 的格式如下：
```
path/to/img1.jpg 50,100,150,200,0 30,50,200,120,3
path/to/img2.jpg 120,300,250,600,2
...
```

对于 VOC 数据集，尝试 `python voc_annotation.py`。

使用 `train.py` 进行训练后，可在使用 `yolo_video.py` 时使用 `--model modelfile` 来使用自定义训练的权重或检查点权重文件。

使用 `--classes class_file` 和 `--anchors anchor_file` 以修改类和 anchor 文件路径。

<br>同时，如果你想使用 YOLOv3 原始预训练权重，可以这样做：
  1. 下载 `darknet53.conv.74` ，可以使用命令 `wget https://pjreddie.com/media/files/darknet53.conv.74`。
  2. 将其重命名为 `darknet53.weights`。
  3. 使用命令 `python convert.py -w darknet53.cfg darknet53.weights model_data/darknet53_weights.h5` 转换为 keras 权重文件。
  4. 在 `train.py` 中使用 `model_data/darknet53_weights.h5`。
