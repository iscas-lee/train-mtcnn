## train mtcnn: a modified version by Zuo Qing from https://github.com/Seanlinx/mtcnn

**(1)请使用[ZQCNN_MTCNN](https://github.com/zuoqing1988/ZQCNN)来进行forward**

**(2)Pnet改为Pnet20需要在你的MTCNN中更改cell_size=20, stride=4**

1920*1080图像找20脸，第一层Pnet20输入尺寸1920x1080，计算量347.9M，原版Pnet输入1152x648，计算量1278.0M

**(3)Rnet保持size=24不变，网络结构变为dw+sep，计算量约为原版1/3**

**(4)Onet暂时没有训练，等陆续更新**

## Introduction
this repository is the implementation of MTCNN in MXnet
* `core`: core routines for MTCNN training and testing.
* `tools`: utilities for training and testing
* `data`: Refer to `Data Folder Structure` for dataset reference. Usually dataset contains `images` and `imglists` 
* `model`: Folder to save training symbol and model
* `prepare_data`: scripts for generating training data for pnet, rnet and onet 

## Useful information
You're required to modify mxnet/src/regression_output-inl.h according to mxnet_diff.patch before using the code for training.

* Dataset format
The images used for training are stored in ./data/dataset_name/images/ 
The annotation file is placed in ./data/dataset_name/imglists/

    * For training:
    Each line of the annotation file states a training sample.  
    The format is:
    [path to image] [cls_label] [bbox_label]  
    cls_label: 1 for positive, 0 for negative, -1 for part face.  
    bbox_label are the offset of x1, y1, x2, y2, calculated by (xgt(ygt) - x(y)) / width(height)  
    An example would be `12/positive/28 1 -0.05 0.11 -0.05 -0.11`.  
    Note that all the strings are seperated by space.

    * For testing:
    Similar to training but only path-to-image is needed. 

* Data Folder Structure (suppose root is `data`)
```
cache (created by imdb)
-- name + image set + gt_roidb
-- results (created by detection and evaluation)
mtcnn # contains images and anno for training mtcnn
-- images
---- 12 (images of size 12 x 12, used by pnet)
---- 24 (images of size 24 x 24, used by rnet)
---- 48 (images of size 48 x 48, used by onet)
-- imglists 
---- train_12.txt
---- train_24.txt
---- train_48.txt
custom (datasets for testing) 
-- images
-- imglists
---- image_set.txt
```

* Scripts to generate training data(from wider face dataset)
    * run wider_annotations/transform.m (or transform.py) to get the annotation file of the format we need.
    * gen_pnet_data.py: obtain training samples for pnet
    * gen_hard_example.py: prepare hard examples. 
	you can set test_mode to "pnet" to get training data for rnet,
	or set test_mode to "rnet" to get training data for onet.
    * gen_imglist.py: ramdom sample images generated by gen_pnet_data.py or gen_hard_example.py to form training set.

## Results
 
![image](https://github.com/Seanlinx/mtcnn/blob/master/fddb_result.png)
    
## License
MIT LICENSE

## Reference
Kaipeng Zhang, Zhanpeng Zhang, Zhifeng Li, Yu Qiao , " Joint Face Detection and Alignment using Multi-task Cascaded Convolutional Networks," IEEE Signal Processing Letter







 
