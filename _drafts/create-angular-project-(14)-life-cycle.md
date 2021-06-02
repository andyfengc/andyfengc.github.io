---
layout: post
title: Create Angular v2+ project (14) - Angular life cycle
author: Andy Feng
---

# Introduction #
Each component has a lifecycle managed by Angular. 

Generally, Angular creates a component and its children, set up data and listens to changes, renders it and children. Then, keep checking it when its data-bound properties change, and destroys it before removing it from the DOM.

Fundamental steps are: 

	constructor() build the component 
	-> ngOnChanges()/ngDoCheck() listen changes of ngModel 
	-> ngOnInit() load huge data 
	-> ngAfterContentInit()/ngAfterContentChecked() listens changes of component's child-content 
	-> ngAfterViewInit()/ngAfterViewChecked() checks component's child-views

Angular application is a tree of components. Each component has an associated view. The global sequence of a component is:

1. constructor
	1. parent constructor
	2. child constructor
1. ngOnChanges() init input properties
1. ngOnInit() init data - view generation started
	1. parent ngOnInit()
	2. child ngOnInit()
1. ngDoCheck() keep checking input properties' changes
	1. check parent
	1. check child
1. ngAfterContentInit() update content of component
	1. parent ngOnInit()
	1. child ngOnInit()
1. ngAfterContentChecked()
1. ngAfterViewInit() update view of component - view generation completed
	1. parent ngAfterViewInit()
	1. child ngAfterViewInit()
1. ngAfterViewChecked()
1. destroy component

# Lifecycle hook of a component
here is the sequence

![](/images/posts/20180925-lifecycle-hooks.jpg)

Hooks for the component

- **constructor**: This is invoked when Angular creates a component or directive by calling new on the class.
- **ngOnChanges** - runs whenever change happens: Invoked every time there is a change in one of the input properties of the component. 
- **ngOnInit** - runs once: Invoked when given component has been created. This hook is only **called once** after the first ngOnChanges. It’s the place to perform complex data initializations shortly after construction. `ngOnInit()` is called after ngOnChanges() was called the first time. ngOnChanges() is called every time inputs are updated by change detection
- ngDoCheck - run whenever change detected: Invoked when the change detector of the given component is invoked. It allows us to implement our own change detection algorithm for the given component. 

Hooks for the components children

- ngAfterContentInit: Invoked after Angular performs any content projection into the components view. Invoked each time after each sub-component added to the component.
- ngAfterContentChecked: Invoked each time the content of the given component has been checked by the change detection mechanism of Angular.
- **ngAfterViewInit** - runs when new sub-views have been rendered: Invoked when the component’s view has been fully initialized. `ngAfterViewInit()` is called after the view is initially rendered. @ViewChild() depends on it and we can access children member in this step.
- ngAfterViewChecked: Invoked each time the view of the given component has been checked by the change detection mechanism of Angular.

Hooks for the component

- ngOnDestroy - runs once: This method will be **invoked once** just before Angular destroys the component. Use this hook to unsubscribe observables and detach event handlers to avoid memory leaks.

# Example #
## component structure
![](/images/posts/20180925-lifecycle-ex1.png)

composite-panel.component.html

	<app-general-composite-info [model]="model" [isEditing]="isEditing" #generalCompositeInfo>
	</app-general-composite-info>
	
	<div class="row">
	    ...
	</div>

composite-panel.component.ts

	@Component({
	    selector: 'app-composite-info-panel',
	    templateUrl: './composite-info-panel.component.html',
	    styleUrls: ['./info.component.scss']
	})
	export class CompositeInfoPanelComponent implements OnInit {
	    private _model: Employee = new Employee();
	    @Input() set model(val: Employee) {
	        this.loadEmployee(val);
	    }
	    get model(): Employee {
	        return this._model;
	    }
	
	    @ViewChild('generalCompositeInfo') generalCompositeInfo: GeneralCompositeInfoComponent;
	
	    constructor() {
	    }
	
	    ngOnInit(): void {
	        ...
	    }
	}

composite.component.html

	<form #compositeForm="ngForm">
	    <app-basic-info [model]="model" #basicInfo></app-basic-info>
	    <app-general-info [model]="model.TblEmployeeFactRel" #generalInfo></app-general-info>
	</form>

composite.component.ts

	@Component({
	    selector: 'app-composite-info',
	    templateUrl: './composite-info.component.html',
	    styleUrls: ['./composite-info.component.scss']
	})
	export class CompositeInfoComponent implements OnInit {
	    private _model: Employee = new Employee(); // class statement
	    @Input() set model(val: Employee) {
	        this.loadEmployee(val);
	    }
	    get model(): Employee {
	        // this._model.TblEmployeeFactRel = this.generalInfo.model; //todo
	        return this._model;
	    }
	
	    @ViewChild('basicInfo') basicInfo: BasicInfoComponent;
	    @ViewChild('generalInfo') generalInfo: GeneralInfoComponent;
	
	    constructor() {
	    }
	
	    ngOnInit(): void {
	        ...
	    }
	}

## Sequence ##:
First loading:

1. composite panel parent component constructor

1. composite panel parent component instance variable

1. composite child component constructor

1. composite child component instance variable

1. grandchild component constructor

1. grandchild component instance variable

1. class statement

1. composite panel parent component
	Input() properties
	ngOnInit()
	
1. composite child component
	Input()
	ngOnInit() 

1. basic grandchild component
	Input()
	ngOnInit() 

1. general grandchild component
	Input()
	ngOnInit() 

Later change/event handling:

1. composite component
	Input()

1. basic component
	Input()

1. general component
	Input()

# FAQ #
## **ngOnInit vs ngAfterViewInit** ##

**ngOnInit** is a life cycle hook called by Angular to indicate that Angular is done creating the component.

**ngAfterViewInit** is also a lifecycle hook that is called after a component's view has been fully generated.

Usually, we initialize data in ngOnInit() for display purpose; process a view via ViewChild() in ngAfterViewInit() i.e. add event handler(s). If we 

	@Component({
	    selector: 'sample',
	    templateUrl: './sample.component.html'
	})
	export class SampleComponent implements OnInit, AfterViewInit {
	
	    @ViewChild(SubComponent) sub: SubComponent;
	
		constructor(){
			// step 1 angular start create the component and sub component
		}
	
		ngOnInit(){
			// step 2 angular complete creating the component the sub components
			// we init or load view data...
			// angular start rendering the view and sub views
		}
	       
	    ngAfterViewInit() {
			// step 3 angular complete rendering the view and sub views
			// we process view components
	        this.sub.doSomething();
			// angular update or process views...
			// angular throws ExpressionChangedAfterItHasBeenCheckedError exception if finds view data changes at this moment
			// we can add setTimeout(() => {}) to defer the data changes if we need to modify view data.
	
	    }
	}

## **ngOnChanges() vs. value changes of ngDoCheck()** ##

ngOnChanges() = value changes of ngDoCheck()

ngDoCheck() is called very often, on each change detection run, we you should normally avoid to use it to avoid performance problems. It will detect the changes/mouseleft on any element, content or view change behavior. 

## **ngAfterContentChecked() vs. ngAfterViewChecked()** ##

AfterContentInit() and AfterContentChecked() hooks on child component instance (AfterContentInit is called only during first check)

- Angular ran change detection for the projected content (ng-content).
- they are called after components external content has been initialized(AfterContentInit) or checked (AfterContentChecked).
- Use it if you need to query projected elements using @ContentChildren decorator.

e.g.

child component

	@Component({
	  selector: 'app-child',
	  template: '<input [(ngModel)]="hero">'
	})
	export class ChildComponent {
	  hero = 'Magneta';
	}

parent component:

	@Component({
	  selector: 'app-parent',
	  template: `
	    <div>-- projected content begins --</div>
	      <ng-content></ng-content>
	    <div>-- projected content ends --</div>
		`
	})
	export class ParentComponent implements AfterContentChecked, AfterContentInit {
	  // Query for a CONTENT child of type `ChildComponent`
	  @ContentChild(ChildComponent) contentChild: ChildComponent;
	
	  constructor() {
	    this.logIt('AfterContent constructor');
	  }
	
	  ngAfterContentInit() {
	    // contentChild is set after the content has been initialized
	    this.logIt('AfterContentInit');
	  }
	
	  ngAfterContentChecked() {
	      this.logIt('AfterContentChecked');
	  }
	
	  private logIt(method: string) {
	    let child = this.contentChild;
	    let message = `${method}: ${child ? child.hero : 'no'} child content`;
	    console.log(message);
	  }
	  // ...
	}

AfterViewInit() and AfterViewChecked() hooks on child component instance (AfterViewInit is called only during first check)

- Angular ran change detection for the view content. 
- Use it if you need to query view elements using @ViewChildren decorator.
- they are called after the component view and its child views has been initialized(AfterViewInit) or checked (AfterViewChecked)
- ngAfterViewChecked is called after the bindings of the view children are checked (it is related to the view only).

e.g.

child component

	@Component({
	  selector: 'app-child-view',
	  template: '<input [(ngModel)]="hero">'
	})
	export class ChildViewComponent {
	  hero = 'Magneta';
	}

parent component
	
	@Component({
	  selector: 'parent-view',
	  template: `
	    <div>-- child view begins --</div>
	      <app-child-view></app-child-view>
	    <div>-- child view ends --</div>`
	   + `
	    <p *ngIf="comment" class="comment">
	      {{comment}}
	    </p>
	  `
	})
	export class ParentComponent implements  AfterViewChecked, AfterViewInit {
	  // Query for a VIEW child of type `ChildViewComponent`
	  @ViewChild(ChildViewComponent) viewChild: ChildViewComponent;
	
	  constructor() {
	    this.logIt('AfterView constructor');
	  }
	
	  ngAfterViewInit() {
	    // viewChild is set after the view has been initialized
	    this.logIt('AfterViewInit');
	  }
	
	  ngAfterViewChecked() {
	      this.logIt('AfterViewChecked');
	  }
	
	  private logIt(method: string) {
	    let child = this.viewChild;
	    let message = `${method}: ${child ? child.hero : 'no'} child view`;
	    console.log(message);
	  }
	  // ...
	}

## "Expression has changed after it was checked" Error ##
This issue is only happens in Angular development mode.

Angular 2+ has verification loops and runs change detection for each component within each loop. 
> ngOnInit, OnChanges and ngDoCheck lifecycle loop
> ngAfterContentInit() + ngAfterContentChecked() lifecycle loop
> ngAfterViewInit lifecycle hook

In each loop, Angular verifies the beginning value(old value) and ending value(new value) of each property and will throw `ExpressionChangedAfterItHasBeenCheckedError` error if the values are different. 

The problem is that model value is changed by the child after the parent has determined and "rendered" this.

e.g. we have two components

parent component

	@Component({
	    selector: 'a-comp',
	    template: `
	        <span>{{name}}</span>``
	        <b-comp [text]="text"></b-comp>
	    `
	})
	export class AComponent implements OnInit {
	    name = 'I am A component';
	    text = 'A message for the child component`;
		constructor() {}
	
	    ngOnInit() {
	    }
	}

child component:

	@Component({
	    selector: 'b-comp',
	    template: `
	        <span>{{text}}</span>
	    `
	})
	export class BComponent implements OnInit {
	    @Input() text;
	
	    constructor(private parent: AComponent) {}
	
	    ngOnInit() {
	        this.parent.text = 'updated text';
	    }
	}

In order to keep proper databindings, Angular uses change detection technique. 

1. Angular checks `A` component.
2. Angular evaluates `text` to `A message for the child component` and passes it down to the `B` component.
3. Angular evaluates `name` to `I am A component`
4. Angular updates the DOM of `A` with these values and puts the evaluated values to the `oldValues` of view in `A` component

	view.oldValues[0] = 'A message for the child component';
	view.oldValues[1] = 'A message for the child component';

1. Angular pass values of `text` and `name` to child B component
1. Angular runs verification lifecyle loops to check values. If value changes, it throws the error `ExpressionChangedAfterItHasBeenCheckedError`

### Solutions: ###
1. asynchronous property update 

		export class BComponent {
		    name = 'I am B component';
		    @Input() text;
		
		    constructor(private parent: AppComponent) {}
		
		    ngOnInit() {
		        setTimeout(() => {
		            this.parent.text = 'updated text';
		        });
		    }
		
		    ngAfterViewInit() {
		        setTimeout(() => {
		            this.parent.name = 'updated name';
		        });
		    }
		}

	here, the setTimeout() function schedules a macrotask then will be executed in the following VM turn. 

	or if the property is observable

		this.parent.text$
		.pipe(
		  delay(0)
		)
		.subscribe(
		() => this.parent.text = 'updated text';
		)

1. forcing additional change detection cycle.

	force another change detection cycle for the parent A component between the first one and the verification phase. And the best place to do it is inside the `ngAfterViewInit()` lifecycle hook as it’s triggered when change detection for all child components have been performed and so they all had possibility to update parent components property:

		export class AppComponent {
		    name = 'I am A component';
		    text = 'A message for the child component';
		
		    constructor(private cd: ChangeDetectorRef) {
		    }
		
		    ngAfterViewInit() {
		        this.cd.detectChanges();
		    }
		}

# Best practice #
Here is a model of typical angular component.

![](/images/posts/20190422-angular-life-cycle-1.png)

It has below behavior:

- the model can be used for adding/updating/viewing purpose. Because it supports add, it has a built-in not-null model.
- the component contains a list of items and a selected item (i.e. model). 
- the component accepts multiple listParams via `Input()` or `Routing` and the change of each param will reload the list
- the `model` property is `Input()` so that the component can be used for view/edit purpose
- reloading the list will re-select/set `model`
- set the `model` property will re-select/set the 'model'

Therefore, we need listen to the changes of the `list` and `model` property. Because any changes of them will trigger to reselect/set the `model`, we use `combineLatest()` to listen to them. Also, we need to keep the last value of `list` and `model` property, we have to set two `BehaviorSubject` objects i.e. `item$`, `list$` to always keep last value and listen.

> param1 change -> load list -> reselect item from the list -> set model
> 
> param2 change -> load list -> reselect item from the list -> set model
> 
> model change -> reselect item from the list -> set model

## Models ##

	export class Component{
		private item$ = BehaviorSubject(null);
		private list$ = BehaviorSubject([]);
		@Input() model(model){ if (model != null) this.item$.next(model.item) }
		@Input() listParam1(val){ if (val != null) this.loadList(val) }
		@Input() listParam2(val){ if (val != null) this.loadList(val) }
		ngAfterViewInit() {
			combineLatest(this.list$, this.item$)
			  .subscribe(([list, item]) => {
			    this.selectItem(item);
			  })
		}
		loadlist() {list$.next(list);}
		selectItem(item){
			if (list contains item) model = item;
			else model = null;
		}
	}

Principle

1. Each entrance to external should cause the change of `$item` or `$list`. 

1. If the component included in parent component, parent shouldn't call the same entrance triggered by multiple different events. per entrance per parent.
	> If there are multiple events in parent invoke the same entrance, each event will trigger the change of `$item` or `$list` and will reselect the item from the list followed by setting the model. That means each event of parent might change the model of component. It is very likely some of events are caused by other children components in parent. In this case, we might have to consider the event sequence in parent in order to ensure some actions go first and some go later. As we know, it is very hard to arrange the execution or subscribe sequence of events. It is very hard to maintain.

1. if a parent pre-rendering method() depends on a child component, we cannot use `*ngIf` to the child component. Angular might throw `ExpressionChangedAfterItHasBeenCheckedError` error 

	e.g.

	parent.component.html

		<div *ngIf="!isLoading">

			<!-- child component -->
		    <app-fact-info [model]="model" [isEditing]="isEditing" #factInfo>
		    </app-fact-info>
		
		    <button type="submit" class="btn btn-primary btn-lg cls-form-control-button cls-form-control-button-enabled"
		        [disabled]="!isValid()"
		        [ngClass]="{'cls-form-control-button-enabled': isValid(), 'cls-form-control-button-disabled': !isValid()}"
		        (click)="update()">Update</button>
		</div>

	parent.component.ts

	    @ViewChild('factInfo') factInfo: FactInfoComponent;
	    public isValid(): boolean {
	        return this.factInfo != null && this.factInfo.isValid();
	    }

	It might throws `ExpressionChangedAfterItHasBeenCheckedError` error. The reason is, Angular keep evaluates the `isValid()` method after `ngOnInit()` in `ngOnChanges()`. When parent is rendering the page in `ngAfterViewInit()` step and call the method, the return value of `isValid()` is `null` because the child component not exists yet. Afterwards, the child was created and appears, the return value of `isValid()` might different from the previous one. Both actions happen in `ngAfterViewInit()` step and triggers the error.

	fixed v1. we always create but hide the child component.

	parent.component.html

		<div [hidden]="isLoading">    	

			<!-- child component -->
		    <app-fact-info [model]="model" [isEditing]="isEditing" #factInfo>
		    </app-fact-info>
		
            <button type="submit" class="btn btn-primary btn-lg cls-form-control-button cls-form-control-button-enabled"
                [disabled]="!isValid()"
                [ngClass]="{'cls-form-control-button-enabled': isValid(), 'cls-form-control-button-disabled': !isValid()}"
                (click)="update()">Update</button>
		</div>

## Complete code: ##
atomic.component.html, please note that we use `ItemId` as the model value

	<select [items]="list" bindLabel="Name" bindValue="ItemId" [(ngModel)]="model.ItemId" required
	    name="item" #item="ngModel" (change)="selectItem($event)" placeholder="Select"
	    type="text">
	</select>

atomic.component.ts

	@Component({
	  selector: 'app-atomic',
	  templateUrl: './atomic.component.html',
	  styleUrls: ['./atomic.component.scss'],
	  providers: []
	})
	export class AtomicComponent implements OnInit, AfterViewInit, OnDestroy{
		private _model = new Model();
		private item$ : BehaviorSubject<number> = new BehaviorSubject(null); // for listening event purpose, can be item or itemId
		@Input() set model(val : any){
			if (val != null){
				//this._model = val; // optional because we already pass `ItemId` to parent/external in `selectItem()` method, pls keep in mind the parent/external always use the `model` property of this component to grab data, don't use the data passed from parent/external
				this.item$.next(val);
			}
		}
		get model() {
			return this._model;
		}

		@Output() modelChanged = new EventEmitter<Model>();
		
		private list = [];
		private list$ = new BehaviorSubject([]);
		
		private _listParam1 : any;
		@Input() set listParam1(val : any) // set only, uni-direction
		{ 
			if (val != null) { 
				this._listParam1 = val;
				loadListByParam1(val); 
			} 
		}
		
		constructor( ) {		
		}
	
		ngOnInit() {
			// load huge data if required
		}
		
		ngAfterViewInit(): void {
			// 
			combineLatest(this.list$, this.item$)
			  .subscribe(([list, item]) => {
			    this.selectItem(item);
			  })
		}
		
		list = [];
		list$ = new BehaviorSubject([]);
		loadListByParam1(param1 : any)
		{
			// load list
			if (param1 == null) {
			  this.list = [];
			  this.list$.next(this.list);
			}
			else {
			  this.list = [];
			  this.service.getListByParam1(param1)
			    .pipe(map(elements => elements.map(element => new Item(element))))
			    .subscribe(elements => {
			        this.list = elements;
			      	this.list$.next(this.list);
			    })
			}
		}
		
		public selectItem(item: any): void {
			if (item != null && item.ItemId != null) { // use `ItemId` to determine if we need to set value of model because ItemId is `ngModel`. use `Item` if we use Item as `ngModel`
				// if list contains item, set _model = item, else set _model = null
				if (this.list.find(i => i.Id == item.Id)) {
					this._model.ItemId = item.Id;
					//this._model = item; // if we use `ItemId` as `ngModel`. This line might trigger infinite change event because it cause model changed!!!, therefore, the `set model()` was re-called and then call `selectItem()` over and over again. we can also use `this._model.Id = new Item(item)` to replace
				}
				else {
					this._model.ItemId = null;
					// or this._model = new Model();
					//this._model = null; // don't use. if we use `ItemId` as `ngModel`. This line might always set model as null if we pass model as a reference from the parent 
				}
			}
			// emit selected item
			this.modelChanged.emit(this._model);
			// this method is an entrance and must cause change of $item
			if (this.item$.getValue() != item) this.item$.next(item); 
		}
		ngOnDestroy(): void {
			if (this.item$ != null) this.item$.unsubscribe();
			if (this.list$ != null) this.list$.unsubscribe();
		}
	}

> Please note if we pass item value to the component when the component is initializing. we should wrapp everthing into setTimeout() in selectItem(item) {} like
> 
>     selectItem(item : any) : void{
>       setTimeout(() => { // select an item until the view is fully rendered
>         // if list contains item, set _model = item, else set _model = null
>         ...
>       });
>     }
>     
>  the reason is: during the child component is initializing and if the parent automatically pass value simultaneously to it, the child component will then call `selectItem()` immediately during the step of `ngAfterViewInit`. But the `selectItem()` method will change the value of `_model`. As we know, in `ngAfterViewInit` step and in dev environment, if Angular finds the model value was changed, it will throw `ExpressionChangedAfterItHasBeenCheckedError` exception. Therefore, we wrap everything into `setTimeout()` and holds the change until Angular fully loaded and checked the view.

pass model value from external/parent

	this.atomicComponent.model = latestData;

逐层传递，每层有自己独立的model，外面传过来的值，只用来设置自己的model，这个model也将被传到外面。尽管外面传值时候可能有nest，每个component设置自己的model不考虑nest，只设好自己的值

# References
[https://angular.io/guide/lifecycle-hooks](https://angular.io/guide/lifecycle-hooks)

[https://medium.com/@zizzamia/the-secret-life-cycle-of-components-ee180a9a42bb](https://medium.com/@zizzamia/the-secret-life-cycle-of-components-ee180a9a42bb)

[https://codecraft.tv/courses/angular/components/lifecycle-hooks/](https://codecraft.tv/courses/angular/components/lifecycle-hooks/)

[https://www.c-sharpcorner.com/article/life-cycle-of-angular-components/](https://www.c-sharpcorner.com/article/life-cycle-of-angular-components/)

[https://blog.angularindepth.com/everything-you-need-to-know-about-the-expressionchangedafterithasbeencheckederror-error-e3fd9ce7dbb4](https://blog.angularindepth.com/everything-you-need-to-know-about-the-expressionchangedafterithasbeencheckederror-error-e3fd9ce7dbb4)

[https://blog.angular-university.io/angular-debugging/](https://blog.angular-university.io/angular-debugging/)