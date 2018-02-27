---
layout: post
title: Nativescript tutorial
author: Andy Feng
---

## Introduction ##

## Installation ##

install node.js
install native script 
	npm install -g nativescript

	tns --version

	tns create project xxx --ng
	tns create xxx --template angular@2.4.0
	tnt platform add android
	tns build android

start a emualator
	tnt emulate android
	tnt run android

	tnt livesync android --emulator --watch

turn off hyper-v
	C:\Windows\system32> bcdedit /set hypervisorlaunchtype off