# 一、YOLOX实现车牌识别

## 1\. 项目介绍

本项目使用了YOLOX-s训练车牌数据集进行识别

## 2\. 实现过程

### 在ubuntu上实现yolox：
一开始是在Ubuntu上进行实现的，但因为cuda出现问题，一直运行不了，重复配了n多次cuda，还是整不好，突然想到之前yolov3在windows上实现是可以的，意思是cuda可以用，所以我改为了在windows上实现，毕竟步骤是一样的。

### 在windows上实现yolox：
1.  首先在github上下载yolox的代码，下载到本地后会有一个yolox-main的文件夹。除此之外，把预训练权重下载下来，我使用的是yolox-s这个网络，所以下载对应的预训练权重。
2.  然后准备数据集，我使用的是VOC格式的数据集，之前我整了个车牌的数据集，里面有50张照片，直接拿来用。网上找一个生成txt的代码，然后生成相关的txt文件，之后在yolox-main文件夹里的datasets文件夹里新建一个VOC文件夹，然后把车牌的数据集丢进去。
3.  修改yolox/data/dataloading.py下一行代码，这行代码是读取数据集的路径，修改成自己的。
4.  修改exps/example/yolox_voc/yolox_voc_s.py，把里面关于txt文档的名字修改一下，以及修改num_classes，也就是类的数量，因为我们只识别车牌，所以我只需要改为1.
5.  修改yolox/data/datasets/voc_classes.py，里面是分类，全部删掉，然后加一个“chepai”
6.  修改yolox/evaluators/voc_eval.py，在开头加个root变量，里面添加xml文件的路径
7.  全部设置成功后，来到tools/train.py，再开头加个import sys;sys.path.append(‘根目录’)。这是因为下面的import会找不到路径，加这句话可以帮他定位。
8.  我们用cmd代替Ubuntu的终端，cd进入yolox-main，输入python tools/train.py -f exps/example/yolox_voc/yolox_voc_s.py -d 1 -b 1 -c yolox_s.pth，其中d为显卡个数，b为批次大小，因为我的显卡实在不行，所以我设置为了1，c是加载预训练权重，然后就可以开始训练了。
9.  纵使数据集很小，我的gpu显存还是不够，所以只能把所有软件都关掉，这时能够训练了。还有一个坑，就是路径中不能有中文，不然运行不了。
10.  训练完得到了权重，我们往assets里丢一张图片测试，cd进入yolox-main，输入python tools/demo.py image -f exps/example/yolox_voc/yolox_voc_s.py -c YOLOX_outputs/yolox_voc_s/latest_ckpt.pth --path assets/1.jpg --conf 0.3 --nms 0.65 --tsize 640 --save_result --device gpu，结果显示效果还不错。