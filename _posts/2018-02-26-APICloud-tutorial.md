---
layout: post
title: APICloud tutorial
author: Andy Feng
---

[Download source](/download/APICloud-demo1.zip)

# Prepare environment #
1. Download and install [APICloud Studio2](https://www.apicloud.com/devtools)
	
	or the complete IDE at **[APICloud SDK](https://docs.apicloud.com/Download/download)**. It includes APICloud Studio、Sublime APICloud Plugins、WebStorm APICloud Plugins、AppLoader、FrameWork、Document

1. Register an account and login in APICloud at [https://www.apicloud.com/console](https://www.apicloud.com/console)

	![](/images/posts/20180225-apicloud-1.png)

1. click create a new app

	![](/images/posts/20180225-apicloud-2.png)

	![](/images/posts/20180225-apicloud-3.png)

	please keep ID on safe and will use it later.

# Create a simple demo #
1. start APICloud Studio, login

1. select the new created app

	![](/images/posts/20180225-apicloud-4.png)


	![](/images/posts/20180225-apicloud-6.png)

	![](/images/posts/20180225-apicloud-7.png)

1. click finish, it will try to create app skeleton

	![](/images/posts/20180225-apicloud-8.png)

	![](/images/posts/20180225-apicloud-9.png)

	- config.xml是配置文件，requried
	- index.html是启动页面，required
	- icon为图标文件目录
	- launch为启动图片目录
	- [More details ](https://docs.apicloud.com/Dev-Guide/widget-package-structure-manual)	

1. Each page is an individual HTML page. i.e. index.html, frame0 - frame<n>.html. Please develop using typical HTML5 + CSS + Javascript techniques. We can also using bootstrap and angularjs.

# Run the app #
There are multiple ways to start and debug the app

## Via WIFI, it is realtime debugging and recommended ##
Make sure cellphone and computer is in the same wifi, open studio ide

1. Install AppLoader

1. right click project > wifi sync ip and port
 
	![](/images/posts/20180225-apicloud-13.png)

1. it display the connection info
	
	![](/images/posts/20180225-apicloud-14.png)

1. In cellphone > open AppLoader > click gray round button > enter connection info

	![](/images/posts/20180225-apicloud-15.png)

	connected successfully, button turns green

	![](/images/posts/20180225-apicloud-16.png)

1. in studio > right click project > wifi global sync

	![](/images/posts/20180225-apicloud-17.png)

1. cellphone display the app

## Local compile ##

![](/images/posts/20180225-apicloud-18.png)

![](/images/posts/20180225-apicloud-19.png)

![](/images/posts/20180225-apicloud-20.png)

then, download the apk and copy to cellphone to install

## Cloud compile ##

![](/images/posts/20180225-apicloud-10.png)

![](/images/posts/20180225-apicloud-11.png)

![](/images/posts/20180225-apicloud-12.png)


# Add a calendar module #
1. studio > right click project > module

	![](/images/posts/20180225-apicloud-21.png)

1. search a module, e.g. calendar module
 
	![](/images/posts/20180225-apicloud-22.png)

1. read the module document

	![](/images/posts/20180225-apicloud-23.png)

1. add module to page

		<body>
		    ...
		    <script type="text/javascript" src="../script/api.js"></script>
		    <script>
		        apiready = function() {
		            var UICalendar = api.require('UICalendar');
		            UICalendar.open({
		                rect: {
		                    x: 30,
		                    y: api.frameHeight / 2 - 170,
		                    w: api.frameWidth - 60,
		                    h: 340
		                },
		                styles: {
		                    bg: 'rgba(0,0,0,0)',
		                    week: {
		                        weekdayColor: '#3b3b3b',
		                        weekendColor: '#a8d400',
		                        size: 12
		                    },
		                    date: {
		                        color: '#3b3b3b',
		                        selectedColor: '#fff',
		                        selectedBg: '#a8d500',
		                        size: 12
		                    },
		                    today: {
		                        color: 'rgb(230,46,37)',
		                        bg: ''
		                    },
		                    specialDate: {
		                        color: '#a8d500',
		                        bg: 'widget://image/a.png'
		                    }
		                },
		                specialDate: [{
		                    date: '2015-06-01'
		                }],
		                switchMode: 'vertical',
		                fixedOn: api.frameName,
		                fixed: false
		            }, function(ret, err) {
		                if (ret) {
		                    alert(JSON.stringify(ret));
		                } else {
		                    alert(JSON.stringify(err));
		                }
		            });
		        }
		    </script>
		</body>

# Add a chat module #


1. studio > right click project > module

	![](/images/posts/20180225-apicloud-21.png)

1. In module store > search a chat module - UIChatBox > add 

	![](/images/posts/20180317-apiclould-1.png)

1. add an empty page, with loaded jquery and bootstrap

		<!DOCTYPE html>
		<html>
		
		<head>
		    <meta charset="UTF-8">
		    <meta name="viewport" content="maximum-scale=1.0,minimum-scale=1.0,user-scalable=0,width=device-width,initial-scale=1.0" />
		    <meta name="format-detection" content="telephone=no,email=no,date=no,address=no">
		    <title>文档</title>
		    <link rel="stylesheet" type="text/css" href="../css/api.css" />
		    <link rel="stylesheet" type="text/css" href="../css/style.css" />
		    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta.2/css/bootstrap.min.css" integrity="sha384-PsH8R72JQ3SOdhVi3uxftmaW6Vc51MKb0q5P2rRUpPvrszuE4W1povHYgTpBfshb" crossorigin="anonymous">
		    <style>
		        .empty {
		            text-align: center;
		            padding: 120px 0;
		        }
		    </style>
		</head>
		
		<body>
		    <div class="text-right"><span id="sender"></span></div>
		    <div class="empty">文档 </div>
    		<script type="text/javascript" src="../script/api.js"></script>
		    <script src="https://code.jquery.com/jquery-1.12.4.js"></script>
		</body>
		
		</html>

1. Read documentation

	![](/images/posts/20180317-apiclould-2.png)
 
1. Add this module

		<body>
		...
			<script>
		     $(function(){
		       apiready = function() {
		       var UIChatBox = api.require('UIChatBox');
		       UIChatBox.open({
		           placeholder: '',
		           maxRows: 4,
		           emotionPath: 'widget://image/emotion',
		           texts: {
		               recordBtn: {
		                   normalTitle: '按住说话',
		                   activeTitle: '松开结束'
		               },
		               sendBtn: {
		                   title: 'send'
		               }
		           },
		           styles: {
		               inputBar: {
		                   borderColor: '#d9d9d9',
		                   bgColor: '#f2f2f2'
		               },
		               inputBox: {
		                   borderColor: '#B3B3B3',
		                   bgColor: '#FFFFFF'
		               },
		               emotionBtn: {
		                   normalImg: 'widget://res/img/chatBox_face1.png'
		               },
		               extrasBtn: {
		                   normalImg: 'widget://res/img/chatBox_add1.png'
		               },
		               keyboardBtn: {
		                   normalImg: 'widget://res/img/chatBox_key1.png'
		               },
		               speechBtn: {
		                   normalImg: 'widget://res/img/chatBox_key1.png'
		               },
		               recordBtn: {
		                   normalBg: '#c4c4c4',
		                   activeBg: '#999999',
		                   color: '#000',
		                   size: 14
		               },
		               indicator: {
		                   target: 'both',
		                   color: '#c4c4c4',
		                   activeColor: '#9e9e9e'
		               },
		               sendBtn: {
		                   titleColor: '#4cc518',
		                   bg: '#999999',
		                   activeBg: '#46a91e',
		                   titleSize: 14
		               }
		           },
		           extras: {
		               titleSize: 10,
		               titleColor: '#a3a3a3',
		               btns: [{
		                   title: '图片',
		                   normalImg: 'widget://res/img/chatBox_album1.png',
		                   activeImg: 'widget://res/img/chatBox_album2.png'
		               }, {
		                   title: '拍照',
		                   normalImg: 'widget://res/img/chatBox_cam1.png',
		                   activeImg: 'widget://res/img/chatBox_cam2.png'
		               }]
		           }
		       }, function(ret, err) {
		           if (ret) {
		               //alert(JSON.stringify(ret));
		               if (ret.msg){
		                 if (ret.msg[0] =='['){ // display emotion
		                   // get emotion mapping
		                   $.getJSON('../image/emotion/emotion.json', function(data){
		                      var pictureName = data.find(x => x.text == ret.msg).name;
		                      $('#sender').append('<br>'+ '<img src="../image/emotion/' +pictureName + '.png">' +'<br>');
		                   })
		                 }
		                 else{ // display message
		                   //document.getElementById('sender').innerHTML += '<br>' + ret.msg+  '<br>';
		                   $('#sender').append('<br>' +ret.msg+'<br>');
		                 }
		               }
		           } else {
		               alert(JSON.stringify(err));
		           }
		       	});
			}
			})
			</script>
		</body>
		</html>

	Please note:

	1. download file and unzip image resources to `root/image/emotion` folder

	1. the emotion image path

		UIChatBox.open({
         placeholder: '',
         maxRows: 4,
         emotionPath: 'widget://image/emotion', // it should be in /image/emotion/emotion-*.png
	
	1. the emotion image mapping (image picture name <-> emotion name) is in emotion.json

			[
			    {"name": "Expression_1","text": "[微笑]"},
			    {"name": "Expression_2","text": "[撇嘴]"},
			    {"name": "Expression_3","text": "[色]"},
			    {"name": "Expression_4","text": "[发呆]"},
			    {"name": "Expression_5","text": "[得意]"},
			    {"name": "Expression_6","text": "[流泪]"},
			    {"name": "Expression_7","text": "[害羞]"},
				...
			]

# FAQ #

1. Cannot create new project and it reports

	![](/images/posts/20180318-apicloud-1.jpg)

	try to uninstall git and reinstall git x64 version


# References #
[https://docs.apicloud.com/Dev-Tools/studio-dev-guide](https://docs.apicloud.com/Dev-Tools/studio-dev-guide)

[https://docs.apicloud.com/Dev-Tools/wifi-debug](https://docs.apicloud.com/Dev-Tools/wifi-debug)

[https://docs.apicloud.com/Dev-Guide/Custom_Loader](https://docs.apicloud.com/Dev-Guide/Custom_Loader)

[http://www.yuanjingzhuang.com/2017/04/19/APICloud框架——融云-UIChatTools实现即时通讯聊天/](http://www.yuanjingzhuang.com/2017/04/19/APICloud框架——融云-UIChatTools实现即时通讯聊天/)
