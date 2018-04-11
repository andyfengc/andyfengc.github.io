---
layout: post
title: Create Angular v2+ project (9) - rxjs
author: Andy Feng
---

# Subject #
Subject is a best practice of publisher-subscriber model in rxjs.

- Observable is an object allows us to emit/publish an event. It has all the Observable operators, and we can subscribe to him.
- Observer is an object allows us to subscribe an observable.
- Subject is both an Observable and Observer allows us to both publish and subscribe.

![](/images/posts/20180411-rxjs-2.png)

Steps to use Subject:

1. create a Subject instance of component: 
	
		const subject = new Subject<datatype>(); 

	datatype can be boolean, string, number...

1. Subject is observable and it means he has all the operators (map, filter, etc. ) and we can subscribe to him.

		subject.subscribe(val => console.log(`First observer ${val}`));

	or

		subject.map(value => `Observer one ${value}`).subscribe(value => {
		  console.log(value);
		});

	here, we subsribe to the subject object. When the subject object changes, the console logs. 

2. Subject is observer and it listens to observable with next(), error(), and the complete() methods. Here is the Subject object methods:

	![](/images/posts/20180411-rxjs-1.png)

		subject.next(event.target.value);

	When we call the next() method of Subject object, it publish the value of event and every subscriber will get this value. 

	We can also trigger error() and complete() of Subject object.

In typical senario, we have the source `Observable` and many `observers`, and multiple observers share the same Observable execution.

# Subject demo #
We will have a textbox. when we enter something inside the textbox, it bounds x seconds and display the result.

1. template: `ng2.component.html`

 		<input type="text" placeholder="Enter message" (keyup)="keyup($event)" [(ngModel)]="message">

1. component: `ng2.component.ts`

		import { Component, OnInit, OnDestroy } from '@angular/core';
		import { Subject } from 'rxjs';
		import { NgModel } from '@angular/forms';
	
		@Component({
		  selector: 'app-ng2',
		  templateUrl: './ng2.component.html',
		  styleUrls: ['./ng2.component.scss']
		})
		export class Ng2Component implements OnInit, OnDestroy {
		
		  constructor() { }
		  
	  	  public message : string;
		  // define a Subject object as observer and it publish string as event objects
		  public messageSubject = new Subject<string>();
		
		  ngOnInit() {
		    // we have some observers subscribe to this subject object
		    this.messageSubject.debounceTime(1000).subscribe(value => {
		      console.log('I waited 1 seconds ', value);
		    })
		    this.messageSubject.debounceTime(2000).subscribe(value => {
		      console.log('I waited 2 seconds ', value);
		    })
		    this.messageSubject.debounceTime(3000).subscribe(value => this.welcome(value))
		  }
		
		  ngOnDestroy() {
		    this.messageSubject.unsubscribe();
		  }
		
		  keyup($event) : void{
		     // it is publishing this value to all the subscribers that have already subscribed to this message
		     this.messageSubject.next(this.message);
		  }
		
		  welcome(value : string) : void{
		    console.log('welcome ', value);
		  }
		}

1. run the demo. enter something.

	![](/images/posts/20180411-rxjs-3.png)

# References #
[reactivex Subject](http://reactivex.io/documentation/subject.html)