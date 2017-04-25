---
title: PHP GD 图片资源类型转为文件流的方式
date: 2016-06-18 18:03:43
tags: GD 图像处理
categories: PHP
---
最近又用了PHP GD库做了图像处理的功能。刚好解决了一个一直困扰我的问题，简单记录下。
问题是这样~
{% codeblock lang:php  %}
$image = imagecreatefrompng($this->back_image_path);
//一系列的图像处理过程
....
//正常情况，直接输出图像文件
$image_file = 'test.png';
imagepng($image,$image_file);
//or 或者输出到浏览器
header('Content-Type: image/png');
imagepng($image);

//now:我想把图片资源用一些第三方或者自己实现的函数上传到云存储上，之前我的做法，是将图片保存本地，然后在读取文件上传，但是这样则就会有大量的IO操作
//那是否直接去掉文件IO的过程呢，答案当然是可以。
//之前在度娘上一直没有找到答案，最后还是问的google，哈哈
//可以这么使用以下方法

ob_start(); // Let's start output buffering.
    imagejpeg($image); //This will normally output the image, but because of ob_start(), it won't.
    $contents = ob_get_contents(); //Instead, output above is saved to $contents
ob_end_clean(); //End the output buffer.

uploadToUFileWithContent($content, $image_name, 'jpg');//第三方函数则就可以调用啦
imagedestroy($image);

{% endcodeblock %}

//好不容易找到解决我问题的帖子，贴上哈哈
http://stackoverflow.com/questions/22266402/how-to-encode-an-image-resource-to-base64