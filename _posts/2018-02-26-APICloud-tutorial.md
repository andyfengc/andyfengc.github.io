---
layout: post
title: APICloud tutorial
author: Andy Feng
---

[Download source](/download/APICloud-demo1.zip)

## Prepare environment ##
1. Download and install [APICloud Studio2](https://www.apicloud.com/devtools)
	
	or the complete IDE at **[APICloud SDK](https://docs.apicloud.com/Download/download)**. It includes APICloud Studio、Sublime APICloud Plugins、WebStorm APICloud Plugins、AppLoader、FrameWork、Document

1. Register an account and login in APICloud at [https://www.apicloud.com/console](https://www.apicloud.com/console)

	![](/images/posts/20180225-apicloud-1.png)

1. click create a new app

	![](/images/posts/20180225-apicloud-2.png)

	![](/images/posts/20180225-apicloud-3.png)

	please keep ID on safe and will use it later.


## Create a simple demo ##
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

## Add module ##
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

## Run the app ##
There are multiple ways to start and debug the app

### Via WIFI, it is realtime test and recommended ###
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

### Local compile ###

![](/images/posts/20180225-apicloud-18.png)

![](/images/posts/20180225-apicloud-19.png)

![](/images/posts/20180225-apicloud-20.png)

then, download the apk and copy to cellphone to install

### Cloud compile ###

![](/images/posts/20180225-apicloud-10.png)

![](/images/posts/20180225-apicloud-11.png)

![](/images/posts/20180225-apicloud-12.png)

## References ##
[https://docs.apicloud.com/Dev-Tools/studio-dev-guide](https://docs.apicloud.com/Dev-Tools/studio-dev-guide)

[https://docs.apicloud.com/Dev-Tools/wifi-debug](https://docs.apicloud.com/Dev-Tools/wifi-debug)

[https://docs.apicloud.com/Dev-Guide/Custom_Loader](https://docs.apicloud.com/Dev-Guide/Custom_Loader)