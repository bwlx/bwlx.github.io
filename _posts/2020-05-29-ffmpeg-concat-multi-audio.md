---
layout: post
title:  "使用 ffmpeg 合并两个不同格式的音频"
---

官方文档在这里，[Concatenate](https://trac.ffmpeg.org/wiki/Concatenate)，Concatenation of files with different codec 一节，讲了两个视频文件合并的例子。于是照猫画虎写了音频的 `ffmpeg -i a.mp3 -i b.mp3 -filter_complex "[0:a] [1:a] concat=n=2:v=0:a=1 [a]" -map [a] -c:a mp3 testfull.mp3` ，注意这里的 `v=0`是必须的，一开始我觉得没有视频，就把视频例子中的 `v=1` 直接去掉了，然后命令一直报错，也不知道哪里错了，后来搜到了[这个](https://superuser.com/questions/841062/concat-two-audio-files-via-ffmpeg-filter-complex) 才弄对了