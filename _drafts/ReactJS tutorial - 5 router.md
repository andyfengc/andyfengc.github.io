---
layout: post
title: ReactJS tutorial - router
author: Andy Feng
---

# Introduction
React doesn't come with a built-in router, but we can easily achieve routing with the `react-router-dom` library.

# Installation
1. install lib
	`npm install react-router-dom axios`
	> `react-router-dom` for the router
	> `axios` for making API calls, optional

2. modify index.js

		import React from 'react';
		import ReactDOM from 'react-dom';
		import App from './app.js'
		import { BrowserRouter } from 'react-router-dom'
		
		ReactDOM.render(
		    <BrowserRouter>
		        <App />
		    </BrowserRouter>
		    , document.getElementById('root'))
	
	To use router, we need to wrap our entire App component in Router. There are two types of routers:
	
	- BrowserRouter - makes pretty URLs like example.com/about.
	- HashRouter - makes URLs with the octothorpe (or hashtag, if you will) that would look like example.com/#about.

1. write routing

	app-routing.js

		import React from 'react'
		import { Route, Routes, Link } from 'react-router-dom'
		import About from './pages/about'
		import Home from './pages/home'
		
		export default function AppRouting() {
		  return (
		    <Routes>
		      <Route path="/" element={<Home/>} />
              <Route path="/blogs" element={<Blogs />} />
              <Route path="/contact" element={<Contact />} />
              <Route path="/*" element={<NoPage />} />
		    </Routes>
		  )
		}

	or

		export default function AppRouting() {
		  return (
		    <Routes>
		      <Route path="/" element={<Layout/>}>
                <Route index element={<Home />} />
                <Route path="blogs" element={<Blogs />} />
                <Route path="contact" element={<Contact />} />
                <Route path="*" element={<NoPage />} />
			  </Route>
		    </Routes>
		  )
		}
	> An application can have multiple `<Routes>`. Our basic example only uses one.
	> `<Route>`s can be nested. The nested <Route>s inherit and add to the parent route. So the `blogs` path is combined with the parent and becomes `/blogs`
	> `Home` component route does not have a path but has an `index` attribute. That specifies this route as the default route for the parent route, which is /.

	add to app.js

		import { Component } from "react";
		import AppRouting from "./app-routing";
		
		class App extends Component {
		  render() {
		    return (
		      <div className="container">
		        <AppRouting/>
		      </div>
		    )
		  }
		}
		export default App;

1. write components

	layout.js

		import { Outlet, Link } from "react-router-dom";
		
		const Layout = () => {
		  return (
		    <>
		      <nav>
		        <ul>
		          <li>
		            <Link to="/">Home</Link>
		          </li>
		          <li>
		            <Link to="/blogs">Blogs</Link>
		          </li>
		          <li>
		            <Link to="/contact">Contact</Link>
		          </li>
		        </ul>
		      </nav>
		
		      <Outlet />
		    </>
		  )
		};
	> `<Outlet>` renders the current route selected.
	> `<Link>` is used to set the URL and keep track of browsing history. Anytime we link to an internal path, we will use `<Link>` instead of `<a href="">`.

	pages/home.js

		export default function Home() {
		  return (<h1>Home</h1>);
		}

	or
	
		const Home = () => {
		  return <h1>Home</h1>;
		};
		export default Home;

	Blogs.js:
	
		const Blogs = () => {
		  return <h1>Blog Articles</h1>;
		};		
		export default Blogs;

	Contact.js:
	
		const Contact = () => {
		  return <h1>Contact Me</h1>;
		};		
		export default Contact;

	NoPage.js:

		const NoPage = () => {
		  return <h1>404</h1>;
		};		
		export default NoPage;

1. result

	![](/images/posts/20220120-react-5.jpg)

1.  add routers and api

	modify app-routing.js

		export default function AppRouting() {
		  return (
		    <Routes>
		      ...
		      <Route path="/github/:id" element={<UserPage/>} />
		    </Routes>
		  )
		}

	add UserPage.js

		import React, { useState, useEffect } from 'react'
		import axios from 'axios'
		import { useParams } from "react-router-dom";
		
		export default function UserPage(props) {
		  // Setting initial state
		  const initialUserState = {
		    user: {},
		    loading: true,
		  }
		
		  // Getter and setter for user state
		  const [user, setUser] = useState(initialUserState)
		  let params = useParams();
		
		  // Using useEffect to retrieve data from an API (similar to componentDidMount in a class)
		  useEffect(() => {
		    const getUser = async () => {
		      // Pass our param (:id) to the API call
		      const { data } = await axios(
		        `https://api.github.com/users/${params.id}`
		      )
		
		      // Update state
		      setUser(data)
		    }
		
		    // Invoke the async function
		    getUser()
		  }, []) // Don't forget the `[]`, which will prevent useEffect from running in an infinite loop
		
		  // Return a table with some data from the API.
		  return user.loading ? (
		    <div>Loading...</div>
		  ) : (
		    <div className="container">
		      <h1>{params.id}</h1>
		
		      <table>
		        <thead>
		          <tr>
		            <th>Name</th>
		            <th>Location</th>
		            <th>Website</th>
		            <th>Followers</th>
		          </tr>
		        </thead>
		        <tbody>
		          <tr>
		            <td>{user.name}</td>
		            <td>{user.location}</td>
		            <td>
		              <a href={user.blog}>{user.blog}</a>
		            </td>
		            <td>{user.followers}</td>
		          </tr>
		        </tbody>
		      </table>
		    </div>
		  )
		}

	result

	![](/images/posts/20220120-react-6.jpg)

# Reference
[React Router](https://reactrouter.com/)

[React Router Tutorial](https://github.com/reactjs/react-router-tutorial)

[Using React Router for a Single Page Application](https://www.taniarascia.com/using-react-router-spa/)

[React Router 使用教程](https://www.ruanyifeng.com/blog/2016/05/react_router.html)