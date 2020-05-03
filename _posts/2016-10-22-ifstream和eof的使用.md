---
layout: post
title:  "ifstream和eof的使用"
date:   2016-10-22 15:40 
excerpt:
tag:
- c++ 
---

{% include toc.html %}

[15:40 ]

# 前言
最近有一测试，需要分别读取不同的图片以及相关的配置信息，以便测试程序功能是否能达到所期望的要求。想这样的需求，为了方便
就是想到了以文件的形式进行读取。

# 动手
开始编码，写完代码如下：

```
// 存储图片相关的信息
struct IMGINFO {
    int count_;
    std::string img_file_name_;
    std::string pmap_;
};


// 读取文件
int read_file(const std::string filename, std::vector<IMGINFO> &result) {

    if (filename.empty()) {
        std::cout << "filename is NULL" << std::endl;
        return -1;
    }
    result.clear();

    std::ifstream file(filename);
    if (!file.is_open()) {
        std::cout << "open file err!" << std::endl;
        return -2;
    }

    // ！读取文件
    IMGINFO imginfo;
    while (!file.eof()) {
        file >> imginfo.img_file_name_;
        file >> imginfo.count_;
        file >> imginfo.pmap_;
        result.push_back(imginfo);
    }
    file.close();
    return 0;
}

```
而文本文件的信息如下：

```
img_0001.jpg
12
(12,12),(13,13)(14,14)
img_0002.jpg
13
(12,12),(13,13)(14,14)
img_0003.jpg
14
(12,12),(13,13)(14,14)
```
**问题来了，最后输出的结果含有几个数据？**：这样会多一个数据，因为用eof判断文件是否结尾是有问题的

# 解决

在使用eof判断文件是否结尾的时候，当发现已无可读写数据，此时流才知道到达了结尾，这时才将标志eofbit修改为true.
也就是说，读取到最后一行(12,12),(13,13)(14,14)时，标志位还没有修改true,接着继续运行，发现已经到文件尾，才设置，这时，
所保存的变量是上一次的值，因此也就会增加一个数据。


# 总结
C++流的使用还是存在坑的～～。