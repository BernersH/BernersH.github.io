---
layout:       post 
title:        "浅尝web app拍照翻转问题"
subtitle:     " \"转还是不转，这是一个问题。\""
date:         2015-12-22 1:12:00
author:       "Bern3rsH"
header-img: "img/purple-bg.jpg"
tags:
      - FE2
      - HTML
      - JS
      - Mobile
---



> “别告诉我那地方在哪，否则世界就小了”

## 初遇
今天在写简历的时候，看着自己写上的那些熟悉而又陌生的名字：兼职联盟，一块推，又想到了暑假里的那段时光。那段时光有苦有乐，但是是十分充实的，毋庸置疑，以后可能有空的时候写篇文章，纪念一下，或者做成阶段性更新的形式，也算是一种个人日记吧。等以后写了，我再把链接贴过来。

偶间想起，当时好像有个问题，没有解决。那是在做[一块推][1]的时候，这也算是我第一个独立完成的前端项目。这是一个web app，页面全是html＋css＋js实现的。那时候js刚自己充分实践，用的是一个也是刚学的学长推荐的[HBuilder编译器][2]和它内置的框架[mui][3]，这是国产的编译器和框架，框架就是为了做接近原生的web app，内置很多对于移动端的优化。其实这个编译器和框架还是挺不错的，支持国产，就是小bug有点多，而且在我的电脑上卡，当时还是用了3年的老联想。。。

当时我们需要做一个用户拍照或者从相册获取并预览的页面，当时也很小白（说的好像现在不是一样。。。），直接在内置的mui demo里找有这个功能的，就把他套过来，当然那个地方只有前端的，没有ajax数据传输的。后来我钻研了一下，加了和后台对接的部分，转换成64位base传给后端的，这里没问题。但是，经过测试发现问题：

* **无论是Android还是iOS，只要选择在拍照之后，横过来拍，出来的预览图片都是翻转90度的 **

这是相关代码:

HTML (嗯，当时要做那个页面要能让用户最多上传9个图片，我也没想到什么好的动态移动那个添加按钮的办法，就用死办法，先在页面上安置9个按钮，然后添加一个，使原来的那个按钮［加号图片］失效，然后再使下一个显示（机智如我。。。））

```
     <div class="things" id="headimg">
	 <div id="get1" class="addphoto1 " onclick="addphoto(1)">
	 <img id="photo1" src="Myicon/add icon.png" />
	 </div>
	 <div id="get2" class="addphoto transparent" onclick="addphoto(2)">
	<img id="photo2" src="Myicon/add icon.png" />
	</div>
	<div id="get3" class="addphoto transparent" onclick="addphoto(3)">
	<img id="photo3" src="Myicon/add icon.png" />
	</div>
	<div id="get4" class="addphoto transparent" onclick="addphoto(4)">
	<img id="photo4" src="Myicon/add icon.png" />
	</div>
	<div id="get5" class="addphoto transparent" onclick="addphoto(5)">
	<img id="photo5" src="Myicon/add icon.png" />
	</div>
	<div id="get6" class="addphoto transparent" onclick="addphoto(6)">
	<img id="photo6" src="Myicon/add icon.png" />
	</div>
	<div id="get7" class="addphoto transparent" onclick="addphoto(7)">
	<img id="photo7" src="Myicon/add icon.png" />
	</div>
	<div id="get8" class="addphoto transparent" onclick="addphoto(8)">
	<img id="photo8" src="Myicon/add icon.png" />
	</div>
	<div id="get9" class="addphoto transparent" onclick="addphoto(9)">
	<img id="photo9" src="Myicon/add icon.png" />
	</div>
	</div>
``` 

JS
 
 ```	        
 mui('body').on('shown', '.mui-popover', function(e) {});
 mui('body').on('hidden', '.mui-popover', function(e) {});
	
	            function addphoto(photoid) {
	                if (hasClass(imgdiv[photoid - 1], 'transparent')) {} 
	                else {
	                    var btnArray = [{
	                        title: "使用手机拍照"
	                    }, {
	                        title: "从相册中选择"
	                    }];
	                    plus.nativeUI.actionSheet({
	                        title: "添加图片",
	                        cancel: "取消",
	                        buttons: btnArray
	                    }, function(e) {
	                        var index = e.index;
	                        switch (index) {
	                            case 0:
	                                break;
	                            case 1:
	                                getImage(photoid);
	                                break;
	                            case 2:
	                                galleryImgs(photoid);
	                                break;
	                        }
	                    });
	                }
	            }
	            var ifphoto = {
	                "ifid": [{
	                    "id": 0
	                }, {
	                    "id": 0
	                }, {
	                    "id": 0
	                }, {
	                    "id": 0
	                }, {
	                    "id": 0
	                }, {
	                    "id": 0
	                }, {
	                    "id": 0
	                }, {
	                    "id": 0
	                }, {
	                    "id": 0
	                }]
	            }
	            var imgflag=0;
	
	            function getImage(photoid) {
	                imgflag=1;
	
	
	                    var cmr = plus.camera.getCamera();
	                    cmr.captureImage(function(p) {
	                        plus.io.resolveLocalFileSystemURL(p, function(entry) {
	                            var localurl = entry.toLocalURL();
	                            var phototempid = 'photo' + photoid;
	                            document.getElementById(phototempid).src = localurl;
	                            appendFile(localurl, photoid);
	                            $(imgdiv[photoid]).removeClass('transparent');
	                        });
	                    });
	                }
	                //相册选取
	
	
	            function galleryImgs(photoid) {
	                imgflag=0;
	
	                plus.gallery.pick(function(e) {
	                    var phototempid = 'photo' + photoid;
	                    document.getElementById(phototempid).src = e.files;
	                    appendFile(e, photoid);
	                    $(imgdiv[photoid]).removeClass('transparent');
	                });
	            }
	            var f1 = null;
	
	            function appendFile(path, photoid) {
	                var img = new Image();
	                img.src = path;
	                img.onload = function() {
	                    var that = this;
	                    //生成比例 
	                    var w = that.width,
	                        h = that.height,
	                        scale = w / h;
	
	                    w = 480 || w;
	                    h = w / scale;
	                    //生成canvas
	
	
	                    var canvas = document.createElement('canvas');
	
	                    var ctx = canvas.getContext('2d');
	                    $(canvas).attr({
	                        width: w,
	                        height: h
	                    });
	                    //旋转
	
	
	
	
	
	    var image1 = new Image();
	
	      // regular rotation about center
	      var xpos = canvas.width/2;
	      var ypos = canvas.height/2;
	      ctx.drawImage(image1, xpos - image1.width / 2, ypos - image1.height / 2);
	
	      ctx.translate(xpos, ypos);
	      ctx.rotate(450* Math.PI / 180);//旋转450度
	      ctx.translate(-xpos, -ypos);
	      ctx.drawImage(image1, xpos - image1.width / 2, ypos - image1.height / 2);
	    //旋转结束
	            };  
	                ctx.drawImage(that, 0, 0, w, h);
	
	                    var base64 = canvas.toDataURL('image/jpeg', 0.6 || 0.6); //1最清晰，越低越模糊。
	                    f1 = base64;
	                    f1 = f1.replace(/data:image\/jpeg;base64,/, "");
	                    var phototempid = 'photo' + photoid;
	                    var pic = document.getElementById(phototempid);
	                    pic.src = base64;
	                    newupload();
	                }
```	                

## 解决

### 一筹莫展

后来，二话不说，毕竟咱在网上最熟练的事儿就是：搜索。我想，今年web app这么火，应该有很多人遇到过这个问题，会有人解答的。但是，真的找不到，当时也不认识大神，也不混社区（现在也没怎么混。。。）所以原来还想搞清原理，但是实在没办法了，之后想了想直接找解决方法。于是想通过判定长与宽的大小来判断照片是竖着拍还是横着拍，然后[通过canvas来实施旋转][4]，但是我也不知道是为什么。后来恰好设计师改了设计稿，上传图片不需要预览了，于是就直接让图片在后端旋转了。于是这个问题一直没解决。。。一晃3，4个月过去了，回忆那时候的岁月，也想到了这个问题。不甘心的滋味涌上心头，于是我又把当时的代码拿出来，测试一下，果然在最新的iOS9上还是这样的。

### 初见曙光
又开始了疯狂查找解决资料的过程，后来忙活了一天（虽然有时候有点懒，但是对于有 bug的情况我还是很上心的，记得有次一个bug卡了5天，饭也没怎么吃，觉也没咋睡，天天熬夜，仿佛执念），终于找到了腾讯TGideas的这篇文章：[《H5拍照应用开发经历的那些坑儿》][5]。里面第一个坑就是照片翻转问题

> 问题描述：
> 手持设备不同方向所拍摄的照片方向不同，照片的方向都会不同，但相册中会自动纠正，这一问题在ios和android中都存在。 
> 问题解决：
> 3.1.1、将图片数据转换成二进制数据，方便获取图片的exif信息；（这里我引入了 [Binary Ajax][6]）
> 3.1.2、获取图片的exif信息；（这里我使用了 [Javascript EXIF Reader][7]）
> 3.1.3、通过图片exif信息，获取图片拍摄时所持设备方向orientation。 
他的代码

```    
// 读取图片数据
	var fr = new FileReader();
	fr.readAsDataURL(file); 
	
	fr.onload = function(fe){ 
	    var result = this.result;
	    var img = new Image();
	 var exif;
	    img.onload = function() {
	        var orientation = exif ? exif.Orientation : 1;
	        // 判断拍照设备持有方向调整照片角度
	        switch(orientation) {
	            case 3: 
	                imgRotation = 180; 
	                break;
	            case 6: 
	                imgRotation = 90; 
	                break;
	            case 8: 
	                imgRotation = 270; 
	            break;
	        }
	    };
	
	    // 转换二进制数据
	    var base64 = result.replace(/^.*?,/,'');
	    var binary = atob(base64); 
	    var binaryData = new BinaryFile(binary);
	
	    // 获取exif信息
	    exif = EXIF.readFromBinaryFile(binaryData);
	
	    img.src = result;
	};
```

Oh,原来是这个exif在搞鬼，懂了懂了。等等，exif是个什么鬼，于是万能的Google告诉了我答案：[exif][8]（照顾国情，我把链接换成了百度百科的）。总的来说，exif就是一种图片格式，但是它比普通的图片格式多了很多参数，eg.光圈 快门 白平衡，还有就是照片的方向，所以腾讯的大大们就获取了这个参数并将他给了照片，照片就可以像从照片库里选出来的一样，可以正常翻转啦。
### 意外发现
在读他们的代码时，我发现一个FileReader()和readAsDataURL()，原谅我浅薄，虽然眼熟，但是不知道怎么用，于是还是查查查：[FileReader.readAsDataURL()][9]（今天才发现[MDN][10]真是好东西，以后一定要多逛逛，把它用起来）页面里面有个demo

HTML

```	
   <input type="file" onchange="previewFile()"><br>
	<img src="" height="200" alt="Image preview...">
```

JS

```	
     function previewFile() {
	  var preview = document.querySelector('img');
	  var file    = document.querySelector('input[type=file]').files[0];
	  var reader  = new FileReader();
	
	  reader.onloadend = function () {
	    preview.src = reader.result;
	  }
	
	  if (file) {
	    reader.readAsDataURL(file);
	  } else {
	    preview.src = "";
	  }
	}
```

为了看看效果，我在现在电脑上测试之后，顺手用Mac版的HBuilder运行了真机测试（目前还不知道现在用的WebStorm如何做移动端的真机测试，Mark一下，以后再查查），我惊奇的发现，它竟然可以直接识别手机，然后有拍照和照片库两个选项，下面甚至可以关联到我的app:File Manager。而且是手机本身好看的UI，如果你看我原来的代码，就会发现，我原来这个弹出选项，还是用的[HTML5+ API Reference][11]的native UI的选项按钮，比较丑不协调。经过测试，横拍后竟然不翻转了，于是我像发现了新大陆，把所有的按钮换为了input。
终于把这个问题解决了，处女座表示终于舒服了。。。不然有的照片正，有的照片反，真的难受，哈哈。

## 后记
虽然这个项目早就交付了，可能因为也没有做多少项目。对于自己的每个项目和包括自己的博客，都像自己的孩子一样，想让他们尽善尽美。毕竟，这都是自己一行行码出来的。可能代码有bug，不够优雅，页面不好看。但是，他们也是我慢慢成长的唯一的见证，那些夜晚唯一的陪伴（包括现在，夜又深了，但是就是想今天解决完问题，今天把博文写完。可能是刚开始，兴趣比较大，但是希望自己能够坚持下去，把自己认为值得和别人分享的，都努力地在自己的一亩三分田力发光发热）。今天一天虽然过的挺累的，但是有着无比的满足感，同时感觉自己真的还欠缺许多，未来还有很多自己要去看学，去看，去敲。世界上真的，总有那么多比你聪明的人还比你努力，一起がんばって （加油）吧！

以上。

－－Bern3rsH写于2015.12.22



[1]:	https://github.com/BernersH/yikuaitui "yikuaitui"
[2]:	http://dcloud.io/ "HBuilder"
[3]:	http://dev.dcloud.net.cn/mui/ "mui"
[4]:	https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial/Transformations "canvas旋转"
[5]:	http://tgideas.qq.com/webplat/info/news_version3/804/7104/7106/m5723/201409/278736.shtml "解决图片翻转"
[6]:	https://code.google.com/p/allyourbase64/source/browse/js/binaryajax.js?r=2cd816ecbebbb7f80012c9b4df4c1fe3292fc66d "Binary Ajax"
[7]:	https://gist.github.com/christopherdebeer/3743287 "exif.js"
[8]:	http://baike.baidu.com/view/22006.htm "exif"
[9]:	https://developer.mozilla.org/en-US/docs/Web/API/FileReader/readAsDataURL "FileReader"
[10]:	https://developer.mozilla.org/en-US/# "MDN"
[11]:	http://www.dcloud.io/docs/api/zh_cn/nativeui.html "HTML5＋"