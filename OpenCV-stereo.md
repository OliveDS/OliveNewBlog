StereoSGBM
StereoSGBM是OpenCV提供的用于立体匹配的类,可将两幅由处在同一水平线的不同摄像机拍摄的图像进行匹配,比较物体在两幅图像中的相对位置,计算求得其视差图.

create()
StereoSGBM类中创建StereoSGBM对象的方法为create()

# create() 创建StereoSGBM对象
# C++
static Ptr<StereoSGBM> cv::StereoSGBM::create (int minDisparity = 0,int  numDisparities = 16,int 	blockSize = 3,int P1 = 0,int P2 = 0,int disp12MaxDiff = 0,int preFilterCap = 0,int uniquenessRatio = 0,int speckleWindowSize = 0,int speckleRange = 0,int mode = StereoSGBM::MODE_SGBM)
# Python
retval = cv2.StereoSGBM_create([，minDisparity [，numDisparities [，blockSize [，P1 [，P2 [，disp12MaxDiff [，preFilterCap [，uniquenessRatio [，speckleWindowSize [，speckleRange [，mode]]]]]]]]]]]])
其参数如下.

参数	含义
minDisparity	最小可能的差异值。通常情况下，它是零，但有时整流算法可能会改变图像，所以这个参数需要作相应的调整。
numDisparities	最大差异减去最小差异。该值总是大于零。在当前的实现中，该参数必须可以被16整除。
BLOCKSIZE	匹配的块大小。它必须是> = 1的奇数。通常情况下，它应该在3..11的范围内。
P1	控制视差平滑度的第一个参数。
P2	第二个参数控制视差平滑度。值越大，差异越平滑。P1是相邻像素之间的视差变化加或减1的惩罚。P2是相邻像素之间的视差变化超过1的惩罚。该算法需要P2> P1。请参见stereo_match.cpp示例，其中显示了一些相当好的P1和P2值（分别为8 number_of_image_channels SADWindowSize SADWindowSize和32 number_of_image_channels SADWindowSize SADWindowSize）。
disp12MaxDiff	左右视差检查中允许的最大差异（以整数像素为单位）。将其设置为非正值以禁用检查。
preFilterCap	预滤波图像像素的截断值。该算法首先计算每个像素的x导数，并通过[-preFilterCap，preFilterCap]间隔剪切其值。结果值传递给Birchfield-Tomasi像素成本函数。
uniquenessRatio	最佳（最小）计算成本函数值应该“赢”第二个最佳值以考虑找到的匹配正确的百分比保证金。通常，5-15范围内的值就足够了。
speckleWindowSize	平滑视差区域的最大尺寸，以考虑其噪声斑点和无效。将其设置为0可禁用斑点过滤。否则，将其设置在50-200的范围内。
speckleRange	每个连接组件内的最大视差变化。如果你做斑点过滤，将参数设置为正值，它将被隐式乘以16.通常，1或2就足够好了。
mode	将其设置为StereoSGBM :: MODE_HH以运行全尺寸双通道动态编程算法。它将消耗O（W H numDisparities）字节，这对640x480立体声很大，对于HD尺寸的图片很大。默认情况下，它被设置为false。
compute()
StereoSGBM类中计算StereoSGBM的方法为compute()

# compute() 计算StereoSGBM
# C++
public void compute(Mat left,Mat right,Mat disp)
# Python
disp = StereoSGBM.compute(left,right)
其参数如下.

参数	含义
left	左目图像矩阵
right	右目图像矩阵
disp	StereoSGBM结果矩阵
计算视差图代码
#!/usr/local/bin/python
# -*- coding: UTF-8 -*-
import cv2
import numpy as np

window_size = 5 # 匹配的块大小 > = 1的奇数
min_disp = 16 # 最小可能的差异值
num_disp = 192-min_disp # 最大差异减去最小差异
blockSize = window_size # 匹配的块大小
uniquenessRatio = 1 # 最佳（最小）计算成本函数值
speckleRange = 3 # 每个连接组件内的最大视差变化
speckleWindowSize = 3 # 平滑视差区域的最大尺寸
disp12MaxDiff = 200 # 左右视差检查中允许的最大差异
P1 = 600 # 控制视差平滑度的第一个参数
P2 = 2400 # 第二个参数控制视差平滑度
imgL = cv2.imread('left1.jpg') # 左目图像
cv2.imshow('Left Image', imgL)
imgR = cv2.imread('right1.jpg') # 右目图像
cv2.imshow('Right Image', imgR)
# 创建StereoSGBM对象并计算
stereo = cv2.StereoSGBM_create(minDisparity = min_disp,numDisparities = num_disp,blockSize = window_size,uniquenessRatio = uniquenessRatio,speckleRange = speckleRange,speckleWindowSize = speckleWindowSize,disp12MaxDiff = disp12MaxDiff,P1 = P1,P2 = P2)
disp = stereo.compute(imgL, imgR).astype(np.float32) / 16.0 # 计算视差图
cv2.imshow('Depth Image SGBM', (disp-min_disp)/num_disp)# 显示视差图结果

key=cv2.waitKey(1) #等待键盘输入,间隔1ms waits for a key event infinitely (when [delay]< 0 ) or for [delay] milliseconds,
if key == 27 : #ESC键的ASCII码
    print "detect ESC"
    break #退出while循环
capture.release() #释放笔记本摄像头
cv2.destroyAllWindows() #关闭所有图像窗口
运行结果
识别摄像头采集图像中的人体并实时跟踪,效果如下.

左目图像:

left.png

右目图像:

right.png

StereoSGBM视差图像:

20190606203623.png

该算法能够将两幅图像进行比较,计算出大体正确的视差图像结果,但物体边缘容易出现深度计算不准确,且存在不少噪声.总体上效果良好,可以进一步改进.
