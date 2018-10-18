---
title: Live2D
date: 2018-10-01 19:42:06
tags:
---

![](https://songxiaofeng.top/blog/images/live2d.png)

<!-- more -->

<link rel="stylesheet" type="text/css" href="https://songxiaofeng.top/live2d/css/live2d.css">
<div id="landlord">
	<div class="message" style="opacity:0"></div>
	<canvas id="live2d" width="280" height="250" class="live2d"></canvas>
	<div class="hide-button">隐藏</div>
</div>
<script type="text/javascript" src="https://cdn.bootcss.com/jquery/2.2.4/jquery.min.js"></script>
<script type="text/javascript">
	var message_Path = '/live2d/'
	var home_Path = 'https://songxiaofeng.top/'
</script>
<script type="text/javascript" src="https://songxiaofeng.top/live2d/js/live2d.js"></script>
<script type="text/javascript" src="https://songxiaofeng.top/live2d/js/message.js"></script>
<script type="text/javascript">
	loadlive2d("live2d", "https://songxiaofeng.top/live2d/model/tia/model.json");
</script>