---
layout: post
title: Create Angular v2+ project (5) - rxjs & observable
author: Andy Feng
---

# Introduction #
[Asynchronous programming](https://en.wikipedia.org/wiki/Asynchrony_(computer_programming)) is an important technique to create web applications. It allows units of work to run separately from the primary application thread and makes main application responsive.

[RxJS](http://reactivex.io/rxjs) is a library for reactive programming using Observables. It help us create asynchronous or callback-based applications quicker and easier. [Angular 2+](https://angular.io) uses [RxJS](https://angular.io/guide/rx-library) to implement asynchronous operations.

# Observables in rxjs #
`Observable` provides support for passing messages between publishers and subscribers in our application. It helps us solve event handling, asynchronous programming issues.

> Observable defines a subscriber function to publish events/values to consumers(observers) subscribe to it.
> 
> An Observable instance begins publishing values only when someone subscribes to it.
> 
> We subscribe by calling the subscribe() method of the instance, passing an observer object to receive the notifications.

`Observer` is a handler for receiving observable notifications implements the Observer interface. It is an object that defines callback methods to handle the three types of notifications that an observable can send:

- `next()`	Required. A handler for each delivered value. Called after execution starts. It defines the actual handler logic.
- `error()`	Optional. A handler for an error notification. An error halts execution of the observable instance.
- `complete()`	Optional. A handler for the execution-complete notification. Delayed values can continue to be delivered to the next handler after execution is complete.

`Observer` is event handler and receives event data published by an observable as a stream.

![](/images/posts/20180411-rxjs-2.png)

# Observables vs Observers vs Subscriptions #
- An observable is a function that produces a stream of values to an observer over time. 
- When you subscribe to an observable, you are an observer.
- An observable can have multiple observers.

## Steps to use observable/observer ##

1. create an `Observable` instance

		// Use the Observable constructor to create an observable instance
		const sequence = new Observable();	

	or

		// define a observable instance. It emits values in a sequence to subscribers(consumers)
		const sequence = Observable.of(...items); // angular 5+
		const sequence = of(...items); // angular 6+
		
	or

		//Converts its argument to an Observable instance. This method is commonly used to convert an array to an observable.
		const sequence = Observable.from(iterable)

	or

	Observable.create(function subscribe(observer) {
            observer.next(item1);
            observer.next(item2);
			observer.next(itemx);
			observer.complete();
        })

		
1. we define a subscriber function inside this instance. this function accepts observer object and put into a list

		const sequence = new Observable((observer) => {
			  // synchronously deliver 1, 2, and 3, then complete
			  observer.next(1);
			  observer.next(2);
			  observer.next(3);
			  observer.complete();
		}

	equivalent to

		const sequence = Observable.of(1, 2, 3);

1. a consumer(observer) calls the subscribe() method of the observable instance. Then pass event handlers

		const sequenceSubscription1 = sequence.subscribe({
			// event handlers
			next() { ... }
			error() { ... }
		});

		const sequenceSubscription2 = sequence.subscribe({
			// event handlers
			next() { ... }
		});

	Subscriber function received an `observer` object, the observer object defines next(), or error()/complete() methods.

1. The observable publish events as a stream and pass values to observers' next() method

## Broadcasting/multicasting ##
Typically, a typical observable creates a new, independent execution for each subscribed observer. When an observer subscribes, the observable wires up a separate event handler and delivers values to that observer.

If we want each subscription of observer(consumer) get the same value, we need multicasting technique. Multicasting is the practice of broadcasting to a list of multiple subscribers in a single execution with the same event data.

We make some changes in above steps. When we subscribe observer to the observable, we add observers to an array(list):

	function multicastSequenceSubscriber() {
	  const seq = [1, 2, 3];
	  // Keep track of each observer (one for every active subscription)
	  const observers = [];
	
	  // Return the subscriber function (runs when subscribe()
	  // function is invoked)
	  return (observer) => {
	    observers.push(observer);
	    return {
	      unsubscribe() {
	        // Remove from the observers array so it's no longer notified
	        observers.splice(observers.indexOf(observer), 1);
	      }
	    };
	  };
	}

	// Create a new Observable that will deliver the above sequence
	const multicastSequence = new Observable(multicastSequenceSubscriber);

	// multiple observers subscribe 
	multicastSequence.subscribe({
	  next(num) { console.log('1st subscribe: ' + num); },
	  complete() { console.log('1st sequence finished.'); }
	});
	multicastSequence.subscribe({
	    next(num) { console.log('2nd subscribe: ' + num); },
	    complete() { console.log('2nd sequence finished.'); }
	  });

# RxJS #
[Reactive programming](https://en.wikipedia.org/wiki/Reactive_programming) is an asynchronous programming paradigm concerned with data streams and the propagation of change. It assumes all events published as a stream and listenable, e.g. keystrokes, an HTTP response, or an interval timer.

`RxJS (Reactive Extensions for JavaScript)` is a library for reactive programming using observables that makes it easier to compose asynchronous or callback-based code. `RxJS` provides an implementation of the Observable type. The library also provides utility functions for creating and working with observables. These utility functions can be used for:

- Converting existing code for async operations into observables
- Iterating through the values in a stream
- Mapping values to different types
- Filtering streams
- Composing multiple streams

## Create observables ##
`RxJS` offers a number of functions that can be used to create new observables. These functions help us create observables from events, timers, promises, and so on.

- Create an observable from a promise

		import { fromPromise } from 'rxjs/observable/fromPromise';
		
		// Create an Observable from a promise
		const data = fromPromise(fetch('/api/endpoint'));
		// Subscribe to begin listening for async result
		data.subscribe({
		 next(response) { console.log(response); },
		 error(err) { console.error('Error: ' + err); },
		 complete() { console.log('Completed'); }
		});

- Create an observable from a counter

		import { interval } from 'rxjs/observable/interval';
		
		// Create an Observable that will publish a value on an interval
		const secondsCounter = interval(1000);
		// Subscribe to the observable
		secondsCounter.subscribe(n =>
		  console.log(`It's been ${n} seconds since subscribing!`));

- Create an observable from an event

		import { fromEvent } from 'rxjs/observable/fromEvent';
		
		const el = document.getElementById('my-element');
		
		// Create an Observable that will publish mouse movements
		const mouseMoves = fromEvent(el, 'mousemove');
		
		// Subscribe to start listening for mouse-move events
		const subscription = mouseMoves.subscribe((evt: MouseEvent) => {
		  // define the event handling logic - Log coords of mouse movements
		  console.log(`Coords: ${evt.clientX} X ${evt.clientY}`);
		
		  // When the mouse is over the upper-left of the screen,
		  // unsubscribe to stop listening for mouse movements
		  if (evt.clientX < 40 && evt.clientY < 40) {
		    subscription.unsubscribe();
		  }
		});

- Create an observable that creates an AJAX request

		import { ajax } from 'rxjs/observable/dom/ajax';
		
		// Create an Observable that will create an AJAX request
		const apiData = ajax('/api/data');
		// Subscribe to create the request
		apiData.subscribe(res => console.log(res.status, res.response));

## Operators ##
`Operators` are functions that build on the observables foundation to enable sophisticated manipulation of collections. e.g. map(), filter(), concat(), and flatMap(). An operator observes the source observable’s emitted values, transforms them, and returns a new observable of those transformed values.

	import { map } from 'rxjs/operators';
	
	Observable.of(1, 2, 3).map((val: number) => val * val).subscribe(x => console.log(x))

RxJS provides many operators (over 150 of them). 

e.g. `catchError` operator that lets us handle known errors from the events of observable. It helps us catch this error and supply a default value, then our stream continues to process values rather than erroring out.

	import { ajax } from 'rxjs/observable/dom/ajax';
	import { map, catchError } from 'rxjs/operators';
	// Return "response" from the API. If an error happens,
	// return an empty array.
	const apiData = ajax('/api/data')
		.pipe(
		  map(res => {
		    if (!res.response) {
		      throw new Error('Value expected!');
		    }
		    return res.response;
		  }),
	  	catchError(err => Observable.of([]))
	)
	.subscribe({
	  next(x) { console.log('data: ', x); },
	  error(err) { console.log('errors already caught... will not run'); }
	});

## Pipe ##
pipes to link operators together and allows us to combine multiple operator functions into a single function.

	import { pipe } from 'rxjs/util/pipe';
	import { filter, map } from 'rxjs/operators';
	
	Observable.of(1, 2, 3, 4, 5)
	.pipe(
	  filter(n => n % 2),
	  map(n => n * n)
	)
	.subscribe(x => console.log(x));

# Subject in rxjs #
Subject is a practice of publisher-subscriber model in RxJS. It allows us to define our own observable and observer.

- Observable is an object allows us to emit/publish an event. It has all the Observable operators, and we can subscribe to him.
- Observer is an object allows us to subscribe an observable.
- Subject is both an Observable and Observer allows us to both publish and subscribe.

Steps to use Subject:

1. create a Subject instance of component: 
	
		const subject = new Subject<data_type/event_type>(); 

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
We will have a textbox. when we enter something inside the textbox, it debounces x seconds and display the result.

1. template: `ng2.component.html`

 		<input type="text" placeholder="Enter message" (keyup)="keyup($event)" [(ngModel)]="message">

1. component: `ng2.component.ts`

		import { Component, OnInit, OnDestroy } from '@angular/core';
		import { Subject } from 'rxjs';
		import { NgModel } from '@angular/forms';
	
		@Component({
		  selector: 'app-ng2',
 
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

## BehaviorSubject vs. ReplaySubject vs. AsyncSubject ##
All of these derives from `Subject` and used for multicasting `Observables`. But they behaves slightly differently.

1. `BehaviorSubject` - It needs an initial value as it always return a value on subscription even if it hasn’t received a next(). At any point, we can retrieve the last value of the subject in a non-observable code using the `getValue()` method or `value` property.
	
		export declare class BehaviorSubject<T> extends Subject<T> {
		    private _value;
		    constructor(_value: T);
		    readonly value: T;
		    getValue(): T;
		    next(value: T): void;
		}
	
	e.g. 
	
		import * as Rx from "rxjs";
		
		const subject = new Rx.BehaviorSubject();
		
		// subscriber 1
		subject.subscribe((data) => {
		    console.log('Subscriber A:', data);
		});
		
		subject.next(Math.random());
		subject.next(Math.random());
		
		console.log(subject.value)
		
		// output
		// Subscriber A: 0.24957144215097515
		// Subscriber A: 0.8751123892486292
		// Subscriber A: 0.1901322109907977
		// 0.1901322109907977
	
	We can also create BehaviorSubjects with an initial value:
	
		import * as Rx from "rxjs";
		
		const subject = new Rx.BehaviorSubject(Math.random());
		
		// subscriber 1
		subject.subscribe((data) => {
		    console.log('Subscriber A:', data);
		});
		
		// output
		// Subscriber A: 0.24957144215097515

	Note that BehaviorSubject always sends the current(latest) value to observers.

1. `ReplaySubject` can send “old” values to new subscribers. It has the extra characteristic that it can record a part of the observable execution and store multiple old values and “replay” them to new subscribers.

	When creating the ReplaySubject we can specify how much values we want to store and for how long we want to store them. e.g. “I want to store the last 2 values, that have been executed in the last second prior to a new subscription”

		import * as Rx from "rxjs";
		
		const subject = new Rx.ReplaySubject(2);
		
		// subscriber 1
		subject.subscribe((data) => {
		    console.log('Subscriber A:', data);
		});
		
		subject.next(Math.random())
		subject.next(Math.random())
		subject.next(Math.random())
		
		// subscriber 2
		subject.subscribe((data) => {
		    console.log('Subscriber B:', data);
		});
		
		// Subscriber A: 0.3541746356538569
		// Subscriber A: 0.12137498878080955
		// Subscriber A: 0.531935186034298
		// Subscriber B: 0.12137498878080955
		// Subscriber B: 0.531935186034298

	Note that when We start subscribing with Subscriber B. The `ReplaySubject` object stores 2 values and it will immediately emit last 2 values to Subscriber B and Subscriber B will log those.

	We can also specify for how long we wanna to store values in the replay subject: 

		`const subject = new Rx.ReplaySubject(2, 100);`

	Here, we only want to store the last 2 values, but no longer than a 100 ms

1. `AsyncSubject`

	While the `BehaviorSubject` and `ReplaySubject` both store values, `BehaviorSubject` stores the lastest value and `ReplaySubject` stores recent values, the `AsyncSubject` works a bit different. It emits the last value of the Observable execution is sent to its subscribers, and only when the execution completes. 

		import * as Rx from "rxjs";
		
		const subject = new Rx.AsyncSubject();
		
		// subscriber 1
		subject.subscribe((data) => {
		    console.log('Subscriber A:', data);
		});
		
		subject.next(Math.random())
		subject.next(Math.random())
		subject.next(Math.random())
		
		// subscriber 2
		subject.subscribe((data) => {
		    console.log('Subscriber B:', data);
		});
		
		subject.next(Math.random());
		subject.complete(); // only the complete() method triggers the response of observers
		
		// Subscriber A: 0.4447275989704571
		// Subscriber B: 0.4447275989704571

	Note that the `AsyncSubject` object emits values multiple times. But only the emitt when it completes can lead subscribers to respond.

# Observables in Angular #

Angular makes use of observables as an interface to handle a variety of common asynchronous operations. e.g.

- The EventEmitter class extends `Observable`, specifically `Subject`
- The HTTP module uses observables to handle AJAX requests and responses.
- The Router and Forms modules use observables to listen for and respond to user-input events.

## Event emitter ##
`EventEmitter`is actually a utility for us to define events within a component. EventEmitter objects can emit an event object via emit() method. Essentially, EventEmitter object is a publisher(observable). When it invokes emit() method, it passes the emitted event object to the next() method of any subscribers(observer). 

source code:

	```
	export declare class EventEmitter<T> extends Subject<T> {
	    __isAsync: boolean;
	    constructor(isAsync?: boolean);
	    emit(value?: T): void;
	    subscribe(generatorOrNext?: any, error?: any, complete?: any): any;
	}	
	```

Steps:

1. define a component and EventEmitter object in it. Usually, we define an EventEmitter object with `@Output()` decorator. 

		@Component({
		  selector: 'zippy',
		  template: `
		  <div class="zippy">
		    <div (click)="toggle()">Toggle</div>
		    <div [hidden]="!visible">
		      <ng-content></ng-content>
		    </div>
		  </div>`})
		 
		export class ZippyComponent {
		  visible = true;
		  @Output() open = new EventEmitter<any>();
		  @Output() close = new EventEmitter<any>();
		 
		  toggle() {
		    this.visible = !this.visible;
		    if (this.visible) {
		      this.open.emit(null);
		    } else {
		      this.close.emit(null);
		    }
		  }
		}

1. In parent component, reference this component

	<zippy (open)="onOpen($event)" (close)="onClose($event)"></zippy>

1. in the parent component .ts file, define two methods `onOpen($event)`, `onClose($event)` to respond to these events.

## HTTP ##
HttpClient returns observables from HTTP method calls. For instance, http.get(‘/api’) returns an observable. we can subscribe to it to get result.

## Async pipe ##
template: 

	<div><code>observable|async</code>: Time: {{ time | async }}</div>

## Router ##
The `ActivatedRoute` is an injected router service that makes use of observables to get information about a route path and parameters. e.g.:

	import { ActivatedRoute } from '@angular/router';
	 
	@Component({
	  ...
	})
	export class Routable2Component implements OnInit {
	  constructor(private activatedRoute: ActivatedRoute) {}
	 
	  ngOnInit() {
	    this.activatedRoute.url
	      .subscribe(url => console.log('The URL changed to: ' + url));
	  }
	}

## Reactive forms ##
Reactive forms have properties that use observables to monitor form control values. The `FormControl` properties `valueChanges` and `statusChanges` contain observables that raise change events. 

We can subscribe to an observable form-control property. Then, whenever the form control changes, it triggers the logic of subscribers. e.g.:

	import { FormGroup } from '@angular/forms';
	 
	@Component({
	  ...
	})
	export class MyComponent implements OnInit {
	  nameChangeLog: string[] = [];
	  heroForm: FormGroup;
	 
	  ngOnInit() {
	    this.logNameChange();
	  }
	  logNameChange() {
	    const nameControl = this.heroForm.get('name');
	    nameControl.valueChanges.forEach(
	      (value: string) => this.nameChangeLog.push(value)
	    );
	  }
	}

# HttpInterceptor #
`HttpInterceptor` was introduced with Angular 4.3. It provides a way to intercept HTTP requests and responses to transform or handle them before passing them along.

1. create a class to implement `HttpInterceptor` interface

	interface HttpInterceptor {
	  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>>
	}

	Most interceptors transform the outgoing request before passing it to the next interceptor in the chain, by calling next.handle(transformedReq). An interceptor may transform the response event stream as well, by applying additional RxJS operators on the stream returned by next.handle().
	
	More rarely, an interceptor may handle the request entirely, and compose a new event stream instead of invoking next.handle(). This is an acceptable behavior, but keep in mind that further interceptors will be skipped entirely.

	e.g.

		import { Injectable } from "@angular/core";
		import { HttpEvent, HttpHandler, HttpInterceptor, HttpRequest } from "@angular/common/http";
		import { Observable } from "rxjs";
		import { finalize } from "rxjs/operators";
		import { LoaderService } from '../services/loader.service';
		@Injectable()
		export class LoaderInterceptor implements HttpInterceptor {
		    constructor(public loaderService: LoaderService) { }
		    intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
		        this.loaderService.show();
		        return next.handle(req).pipe(
		            finalize(() => this.loaderService.hide())
		        );
		    }
		}

1. Add `HttpInterceptor` to app.module.ts

		providers: [
		    ...
		    { provide: HTTP_INTERCEPTORS, useClass: LoaderInterceptor, multi: true }
		]


# References #
[reactivex Subject](http://reactivex.io/documentation/subject.html)

[Angular RxJS](https://angular.io/guide/observables)

[Angular observables](https://angular.io/guide/observables-in-angular)

[rxjs operations](https://github.com/btroncone/learn-rxjs/blob/master/operators/complete.md)