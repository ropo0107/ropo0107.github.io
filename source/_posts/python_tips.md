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