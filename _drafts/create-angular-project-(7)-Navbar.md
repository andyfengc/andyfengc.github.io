---
layout: post
title: Create Angular v2+ project (2) - Navbar
author: Andy Feng
---

# Introduction #
There are 2 approaches to create navbar

# Approach 1: bootstrap + jquery
1. install bootstrap

	`npm install bootstrap --save`

	`npm install jquery --save`

	then, add to angular.json > style section
 
		"architect": {
		  "build": {
		    [...], 
		    "styles": [
		      "src/styles.css", 
		        "node_modules/bootstrap/dist/css/bootstrap.min.css"
		      ],
		      "scripts": [
		        "node_modules/jquery/dist/jquery.min.js",
		        "node_modules/bootstrap/dist/js/bootstrap.min.js"
		      ]
		    },

1. create a nav bar component

	`ng g c nav-bar`

1. add below to nav-bar.html

		<nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
		    <a class="navbar-brand" href="#">Fixed navbar</a>
		    <button class="navbar-toggler collapsed" type="button" data-toggle="collapse" data-target="#navbarCollapse" aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
		      <span class="navbar-toggler-icon"></span>
		    </button>
		    <div class="navbar-collapse collapse" id="navbarCollapse" style="">
		      <ul class="navbar-nav mr-auto">
		        <li class="nav-item active">
		          <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
		        </li>
		        <li class="nav-item">
		          <a class="nav-link" href="#">Link</a>
		        </li>
		        <li class="nav-item">
		          <a class="nav-link disabled" href="#">Disabled</a>
		        </li>
		      </ul>
		      <form class="form-inline mt-2 mt-md-0">
		        <input class="form-control mr-sm-2" type="text" placeholder="Search" aria-label="Search">
		        <button class="btn btn-outline-success my-2 my-sm-0" type="submit">Search</button>
		      </form>
		    </div>
		  </nav>

	![](/images/posts/20200901-angular-2.png)
	![](/images/posts/20200901-angular-6.png)
	![](/images/posts/20200901-angular-5.png)

	or

		<nav class="navbar navbar-expand-md bg-dark navbar-dark fixed-top">
		  <a class="navbar-brand" href="#">Angular Bootstrap Demo</a>
		  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse" aria-controls="navbarCollapse"
		    aria-expanded="false" aria-label="Toggle navigation">
		    <span class="navbar-toggler-icon"></span>
		  </button>
		  <div class="collapse navbar-collapse" id="navbarCollapse">
		    <ul class="navbar-nav mr-auto">
		
		      <li class="nav-item">
		        <a class="nav-link" routerLink="/home">Home</a>
		      </li>
		      <li class="nav-item">
		        <a class="nav-link" routerLink="/contact-list">Contacts</a>
		      </li>
		      <li class="nav-item">
		        <a class="nav-link" routerLink="/contact-create">Create</a>
		      </li>
		
		    </ul>
		  </div>
		</nav>

	![](/images/posts/20200901-angular-3.png)
	![](/images/posts/20200901-angular-4.png)
	![](/images/posts/20200901-angular-7.png)

# Approach 2: pure bootstrap
Here are 2 reasons for ignoring using JQuery with Angular

1. Overhead. jQuery will add 30KB to your page size.
1. jQuery can be tricky to configure with bundlers like Webpack.

Steps:

1. Add below to nav-bar.component.ts

		import { Component, OnInit, ViewEncapsulation, AfterViewInit, Output, EventEmitter, HostListener } from '@angular/core'
		import { Router } from '@angular/router';
		
		@Component({
		  selector: 'app-nav-bar',
		  templateUrl: './nav-bar.component.html',
		  styleUrls: ['./nav-bar.component.scss']
		})
		export class NavBarComponent implements OnInit {
		  navbarOpen = false;
		  public clicked = false;
		  _el: any;
		
		  user : any = {};
		
		  constructor(
		    private router: Router,
		  ) { }
		
		  toggleNavbar() {
		    this.navbarOpen = !this.navbarOpen;
		  }
		
		  ngOnInit() { }
		
		  onClick(event): void {
		    event.preventDefault();
		    event.stopPropagation();
		    this.clicked = true;
		  }
		
		  @HostListener('document: click', ['event'])
		  private clickedOutside(event): void {
		    if (this.clicked) {
		      this._el.nativeElement.querySelector('.dropdown - menu').classList.toggle('show');
		    }
		  }
		
		  logout(){}
		}

	* `navbarOpen` is used to control whether the menu collapsed or not
	* toggleNavbar() is used to collapse or expand menu

1. nav-bar.html

		<nav class="navbar navbar-expand-lg navbar-light bg-light">
		    <a class="navbar-brand" href="#">
		        <img src="https://www.google.com/images/branding/googlelogo/2x/googlelogo_color_92x30dp.png" class="img-fluid" style="width:200px; height:50px" alt="">
		    </a>
		    <button class="navbar-toggler" type="button" (click)="toggleNavbar()">
		        <span class="navbar-toggler-icon"></span>
		    </button>
		    <div class="collapse navbar-collapse" [ngClass]="{ 'show': navbarOpen }">
		        <ul class="navbar-nav ml-auto">
		            <li class="nav-item dropdown ml-auto" appdropdown >
		                <a class="nav-link dropdown-toggle" href="" onclick="return false;" id="navbarDropdownMenuLink"
		                    data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
		                    Home
		                </a>
		                <div class="dropdown-menu dropdown-menu-right avatar" aria-labelledby="navbarDropdownMenuLink">
		                    <div class="sidebar-scroll" id="list-group">
		                        <a class="dropdown-item chat" [routerLink]="['/my', user.username]" href="#!">
		                            <span class="d-xs-none username">Profile</span>
		                        </a>
		                        <a class="dropdown-item chat" [routerLinkActive]="['active']"
		                            [routerLinkActiveOptions]="{exact: true}" [routerLink]="['/settings']" href="#!">
		                            <span class="d-xs-none username">Settings</span>
		                        </a>
		                        <a class="dropdown-item chat" (click)="logout()" href="#!">
		                            <span class="d-xs-none username">Logout</span>
		                        </a>
		                    </div>
		                </div>
		            </li>
		        </ul>
		    </div>
		</nav>

1. demo

	![](/images/posts/20200901-angular-8.png)
	![](/images/posts/20200901-angular-9.png)
	![](/images/posts/20200901-angular-10.png)

# References
[Styling An Angular Application With Bootstrap](https://www.smashingmagazine.com/2019/02/angular-application-bootstrap/)

[How to Build a Responsive Bootstrap 4 Navbar in Angular Without JQuery](https://medium.com/@haykoyaghubyan/how-to-build-a-responsive-bootstrap-4-navbar-in-angular-without-jquery-2c5a2339bbfb)
