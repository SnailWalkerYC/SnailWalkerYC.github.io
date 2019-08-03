---
layout: post
title: Qt image and OpenCV image
date: 2019-07-02
Author: 来自中世界
categories: [technology]
tags: [engineering]
comments: true
---

- QImage and cv::mat transform
During the transformation between QImage and cv::mat, be careful about copy style. Deep copy is recommended  otherwise there would be memory leaky problem.
 

#### QImage to cv::mat
```
cv::Mat QImage2Mat(QImage const& src)
{
     cv::Mat tmp(src.height(),src.width(),CV_8UC3,(uchar*)src.bits(),src.bytesPerLine());
     cv::Mat result; // deep copy just in case (my lack of knowledge with open cv)
     cvtColor(tmp, result,CV_BGR2RGB);
     return result.clone();
}
```
clone is needed. 

#### cv::mat to QImage
```
QImage Mat2QImage(cv::Mat const& src)
{
     cv::Mat temp; 
     cvtColor(src, temp,CV_BGR2RGB); // cvtColor Makes a copt, that what i need
     QImage dest((const uchar *) temp.data, temp.cols, temp.rows, temp.step, QImage::Format_RGB888);
     dest.bits(); // enforce deep copy, see documentation
     // of QImage::QImage ( const uchar * data, int width, int height, Format format )
     return dest.copy();
}
```

In  this function, using copy function as deep copy to eliminate memory leakage problem.

#### uint8_t* to cv::mat
```
cv::Mat frame_obj = cv::Mat(IMAGE_HEIGHT, IMAGE_WIDTH, CV_8UC3, frame_arr).clone();
```

Be careful about clone, otherwise it will be memory leakage with shallow copy without clone.

#### Ref links for format transform:
> QImage introduction: http://doc.qt.io/qt-5/qimage.html
>
> cv::mat introduction:
> reference link: https://blog.csdn.net/zyh821351004/article/details/46646735
> 
> http://www.cnblogs.com/grandyang/p/5602360.html
>
> https://stackoverflow.com/questions/5026965/how-to-convert-an-opencv-cvmat-to-qimage


#### Qt5 Pro config
- c++ version
> CONFIG += c++14 
>
> QMAKE_CXXFLAGS += -std=gnu++14
- QQuickView add to pro
> QT += core gui widgets multimedia multimediawidgets network qml quick 
- flann and openCV confilict
```
flann/util/serialization.h class std::unordered_map<unsigned int, std::vector<unsigned int> >' has no member named 'serialize'
``` 
> Flann head file should be ahead of Opencv head file. 
- std has no member make_unique
> This is the C++ 14 feature, so g++ version should be higher or equal to c++14.
- add .a and .so file in pro
> Add into LIBS += directoly. [4] 
 
#### Ref links:
```
https://forum.qt.io/topic/46381/solved-linux-make_unique-and-gcc-compiler/5
https://github.com/mariusmuja/flann/issues/214
https://stackoverflow.com/questions/42504592/flann-util-serialization-h-class-stdunordered-mapunsigned-int-stdvectorun
http://www.qtcentre.org/threads/46678-How-to-add-an-external-a-library-in-Qt-Creator-project-via-GUI
```
