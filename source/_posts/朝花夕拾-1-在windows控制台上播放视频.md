---
title: 朝花夕拾(1) - 在windows控制台上播放视频
date: 2019-05-02 21:13:26
tags:
---

![](https://songxiaofeng.top/blog/images/console.png)

<!-- more -->

------



# 1. 引言

1998年8月，[ZUN](https://zh.moegirl.org/ZUN)推出了东方Project PC-98平台的第四作《東方幻想鄉　～ Lotus Land Story.》，其中 **Bad apple!!** 是第3关的道中曲。

**Bad apple** 作为东方Project系列的神曲之一，多年来相关的二次同人创作视频层出不穷。其中最为经典的便是2009年niconico上的[【東方】Bad Apple!!　ＰＶ【影絵】](https://www.nicovideo.jp/watch/sm8628149)。

由于n站被墙，下面给出一个b站搬运版本。

<div style="text-align: center;">
<iframe width="800"  height= "600"  frameborder="0" 
src="//player.bilibili.com/player.html?aid=47027321&cid=82363459&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
</div>
上面这个视频曾经风靡一时，各种屠版，然而这只是一个开始。。。

。。。



大佬们闲的蛋疼，于是各种版本的Bad apple就出来了：

* [windows控制台版的badapple](<https://www.bilibili.com/video/av1113379?from=search&seid=18273092384100405411>)
* [linux控制台版的badapple](<https://www.bilibili.com/video/av10527111?from=search&seid=17580766857081712967>)
* [38元老年机的badapple](<https://www.bilibili.com/video/av27937237?from=search&seid=14888378526205561327>)
* [任务管理器版的badapple](<https://www.bilibili.com/video/av2281028?from=search&seid=6810303524594863576>)
* [扫雷版badapple](<https://www.bilibili.com/video/av5990842?from=search&seid=2061984435141965197>)
* [mc版的badapple](<https://www.bilibili.com/video/av2441922?from=search&seid=8191068798308544768>)
* [LED的badapple](<https://www.bilibili.com/video/av4623323?from=search&seid=381475200735632050>)
* [LCD的badapple](<https://www.bilibili.com/video/av15215533?from=search&seid=13942812813283652243>)
* [示波器上的badapple](<https://www.bilibili.com/video/av33002230?from=search&seid=3776602408300451326>)
* [计算器上的badapple](<https://www.bilibili.com/video/av43309892?from=search&seid=14888378526205561327>)
* [记事本上的badapple](<https://www.bilibili.com/video/av42800343?from=search&seid=12834342446229460882>)
* [84年IBM古董机的badapple](<https://www.bilibili.com/video/av9474552?from=search&seid=13942812813283652243>)
* [入侵学校的badapple](<https://www.bilibili.com/video/av11588741?from=search&seid=17687602156275824988>)
* [学校图书馆查询机的badapple](<https://www.bilibili.com/video/av2257382?from=search&seid=13942812813283652243>)
* [学校草坪的badapple](<https://www.bilibili.com/video/av27327484?from=search&seid=13942812813283652243>)
* [学校冲卡机的badaple](<https://www.bilibili.com/video/av47201664?from=search&seid=17514128798342475527>)
* [学校礼堂的badapple](<https://www.bilibili.com/video/av41759242?from=search&seid=17687602156275824988>)

反正有屏幕的地方就有badapple，没有屏幕的地方也有badapple。。。

<br>

---

# 2. 在windows控制台上播放badapple

和前面各色各样的badapple比起来，在windows控制台上播放badapple应该算是最简单、最容易实现的一个了。

我17年的时候也曾经写过一个windows控制台版本的badapple，下面通过问答的形式来简单介绍一下实现思路，以及其中可能遇到的问题。

<br>

---

## `如何在控制台上打印视频帧？`

思路是： 将视频帧转化为字符串，然后使用printf打印到控制台上。

由于原视频只有黑白两色，我们可以用两个字符代表黑白两种颜色：“@”代表白色，“ ” (空格)代表黑色。效果如下：

![](https://songxiaofeng.top/blog/images/1556872259376.png)

<br>

---

## `如何获取视频帧？`

使用opencv中VideoCapture类提供的方法。VideoCapture类的`>>`操作符可以提取一个视频帧。代码如下：

```
	cv::VideoCapture videoCapture;
	videoCapture.open("badapple.mp4"); //打开视频
	
	cv::Mat frame;
	VideoCapture >> frame; //获取视频帧，数据存储到cv::Mat中
```
<br>

---

## `如何将视频帧转化为字符串？`

首先将视频帧由3通道的rgb模式转化为单通道的灰度模式，从而方便后续的二值化处理。

```
cv::cvtColor(frame, frame, CV_RGB2GRAY);
```

由于原视频帧分辨率可能比较高，而控制台上分辨率较低，所以需要调整视频帧大小。

```
cv::resize(frame, frame, cv::Size(config::RESIZE_WIDTH, config::RESIZE_HEIGHT), (0, 0), (0, 0), cv::INTER_LINEAR);
```

然后我们可以遍历每一个像素，将视频帧转换为字符串。 每一个像素值由0-255之间的一个灰度值表示，我们可以设定一个阈值（假设取120），那么如果一个像素的灰度值大于120，则代表白色，我们用“@”表示，反之则代表黑色，用“ ”表示。

```
	std::string frameStrBuf;
	for (int i = 0; i < frame.rows; ++i)
	{
		const uint8_t * pData = frame.ptr<uint8_t>(i);
		for (int k = 0; k < frame.cols; ++k)
		{
			if (pData[k] > config::BIN_THRESHOLD)
				frameStrBuf += "@";
			else
				frameStrBuf += " ";
		}
		frameStrBuf += "\n";
	}
```

最后将字符串输出到控制台上。

```
printf("%s", frameStrBuf.c_str());
```

<br>

---

## `为什么使用printf?`

不同的编译器实现可能不同，但在vs2017环境下， printf的效率远远大于std::cout。

<br>

---

## `如何刷新控制台？`

最简单的思路是直接调用`system("cls")`，但这样会造成严重的闪屏效果，帧与帧之间不连贯。

解决方案是：不是“清除”，而是“覆盖”。

```
	HANDLE hConsole = GetStdHandle(STD_OUTPUT_HANDLE);
	COORD cursorPos = { 0, 0 };
	SetConsoleCursorPosition(hConsole, cursorPos);
	//绘制新的一帧
```

每一次新绘制的内容将会覆盖原来的内容，这样就不会闪屏。

<br>

---

## `如何修改控制台窗口大小？`

使用`mode con cols=? lines=?`命令。

```
	char cmd[128];
	sprintf(cmd, "mode con cols=%d lines=%d", config::RESIZE_WIDTH + config::BORDER_SIZE, config::RESIZE_HEIGHT + config::BORDER_SIZE);
	system(cmd);
```

<br>

---

## `如何控制帧率？`

思路：计时 + 睡眠

使用std::chrono::high_resolution_clock计时，配合std::this_thread::sleep_for实现。

```
	double deltaTimeInMills = 1000.0 / frameRate;
	for (int i = startFrame; i <= endFrame; ++i)
	{
		auto startTime = std::chrono::high_resolution_clock::now();
		__printVideoFrame(videoCapture);
		auto endTime = std::chrono::high_resolution_clock::now();

		auto duration = std::chrono::duration_cast<std::chrono::duration<double, std::ratio<1, 1000>>>(endTime - startTime).count();

		if (duration < deltaTimeInMills) std::this_thread::sleep_for(std::chrono::milliseconds((int)(deltaTimeInMills - duration)));

		std::cout << "FPS: " << 1000.0 / std::max(duration, deltaTimeInMills) << std::endl;
	}
```

<br>

---

最后给出源码链接：<https://github.com/s-toy/ConsoleVideoPlayer>