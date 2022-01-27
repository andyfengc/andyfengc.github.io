---
layout: post
title: ReactJS tutorial - router
author: Andy Feng
---

# Introduction
`MUI` is a React UI framework(library). It provides a robust, customizable, and accessible library of foundational and advanced components, enabling you to build your own design system and develop React applications faster.

# Steps
1. Install

	`npm install @mui/material @emotion/react @emotion/styled`

1. add a button

	app.js

		import * as React from 'react';
		import ReactDOM from 'react-dom';
		import Button from '@mui/material/Button';
		
		function App() {
		  return <Button variant="contained">Hello World</Button>;
		}
		
		ReactDOM.render(<App />, document.querySelector('#app'));

	index.html
	...
	<meta name="viewport" content="initial-scale=1, width=device-width" />

	![](/images/posts/20220120-react-10.jpg)

# Reference
[mui](https://mui.com/)

[mui 中文](https://mui.com/zh)