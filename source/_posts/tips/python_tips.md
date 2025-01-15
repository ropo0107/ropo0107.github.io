---
title: python中的tips
categories: [编程]
tags:   [python, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-03-19 02:51:55
---

## Python中读写pkl文件
- 不采用压缩方式

    ```python
    import pickle

    #write
    pack = "xxx"
    with open("demo.pkl",'wb') as f:
            pickle.dump(pack,f)

    #read
    with open("demo.pkl",'rb') as f:
            data = pickle.load(f)
    ```

- 采用压缩方式

    ```python
    import bz2
    import pickle

    #write
    pack = "xxx"
    with bz2.BZ2File("demo.pkl",'wb') as f:
            pickle.dump(pack,f)

    #read
    with bz2.BZ2File("demo.pkl",'rb') as f:
            data = pickle.load(f)
    ```
两种方式数据占用量差别在3-4倍，可以显著节省空间。

## 保存图片

```python
# matplotlib
import matplotlib.image as Image
Image.imsave("save_path.jpg", left_shoulder_rgb)
```
保存值范围:0~1
保存格式为:RGB

```python
# cv2
import cv2
cv2.imwrite("save_path.jpg",rgb,[int(cv2.IMWRITE_JPEG_QUALITY),100])

```
保存值范围:0~255
保存格式为:RBG

## tensor
np.array 转成 tensor两种方法

```python
# print("numpy",color.shape)
# numpy shape(240*320*3)
# cv2.imwrite("/home/sunshine/color"+str(index)+".jpg",color,[int(cv2.IMWRITE_JPEG_QUALITY),100])


# to (3*240*320) 
color = trans.ToTensor()(color) 
sensor = trans.ToTensor()(sensor)

# to (240*320*3)
# color = torch.from_numpy(color)
# sensor = torch.from_numpy(sensor)
```