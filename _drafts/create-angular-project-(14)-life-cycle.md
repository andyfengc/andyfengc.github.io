---
layout: post
title: Create Angular v2+ project (14) - Angular life cycle
author: Andy Feng
---

# Introduction #
Each component has a lifecycle managed by Angular. 

Generally, Angular creates a component, renders it, creates and renders its children, checks it when its data-bound properties change, and destroys it before removing it from the DOM.

Angular application is a tree of components. Each component has an associated view. The global sequence of a component is:

1. constructor
	1. parent constructor
	2. child constructor
1. ngOnChanges() init input properties
1. ngOnInit() init data - view generation started
	1. parent ngOnInit()
	2. child ngOnInit()
1. ngDoCheck() keep checking input properties changes
1. ngAfterContentInit() update content of component
	1. parent ngOnInit()
	1. child ngOnInit()
1. ngAfterViewInit() update view of component - view generation completed
	1. parent ngAfterViewInit()
	1. child ngAfterViewInit()
1. keep checking component's input properties changes
	1. check parent
	1. check child
1. destroy component

# Lifecycle hook of a component
here is the sequence

![](/images/posts/20180925-lifecycle-hooks.jpg)

Hooks for the component

- **constructor**: This is invoked when Angular creates a component or directive by calling new on the class.
- **ngOnChanges** - runs whenever change happens: Invoked every time there is a change in one of the input properties of the component. 
- **ngOnInit** - runs once: Invoked when given component has been created. This hook is only **called once** after the first ngOnChanges. It’s the place to perform complex data initializations shortly after construction
- ngDoCheck - run whenever change detected: Invoked when the change detector of the given component is invoked. It allows us to implement our own change detection algorithm for the given component. 

Hooks for the components children

- ngAfterContentInit: Invoked after Angular performs any content projection into the components view. Invoked each time after each sub-component added to the component.
- ngAfterContentChecked: Invoked each time the content of the given component has been checked by the change detection mechanism of Angular.
- **ngAfterViewInit** - runs when new sub-views have been rendered: Invoked when the component’s view has been fully initialized.
- ngAfterViewChecked: Invoked each time the view of the given component has been checked by the change detection mechanism of Angular.

Hooks for the component

- ngOnDestroy - runs once: This method will be **invoked once** just before Angular destroys the component. Use this hook to unsubscribe observables and detach event handlers to avoid memory leaks.

## ngOnInit vs ngAfterViewInit
**ngOnInit** is a life cycle hook called by Angular2 to indicate that Angular is done creating the component.

**ngAfterViewInit** is also a lifecycle hook that is called after a component's view has been fully generated.

Usually, we initialize data in ngOnInit() for display purpose; process a view via ViewChild() in ngAfterViewInit() i.e. add event handler(s) 

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
	    private _model: Employee = new Employee();
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

1. constructor

3. composite panel component
	Input() properties
	ngOnInit()
	
1. composite component
	Input()
	ngOnInit() 

1. basic component
	Input()
	ngOnInit() 

1. general component
	Input()
	ngOnInit() 

Later change/event handling:

1. composite component
	Input()

1. basic component
	Input()

1. general component
	Input()

# References
[https://angular.io/guide/lifecycle-hooks](https://angular.io/guide/lifecycle-hooks)

[https://medium.com/@zizzamia/the-secret-life-cycle-of-components-ee180a9a42bb](https://medium.com/@zizzamia/the-secret-life-cycle-of-components-ee180a9a42bb)

[https://codecraft.tv/courses/angular/components/lifecycle-hooks/](https://codecraft.tv/courses/angular/components/lifecycle-hooks/)

[https://www.c-sharpcorner.com/article/life-cycle-of-angular-components/](https://www.c-sharpcorner.com/article/life-cycle-of-angular-components/)

[https://blog.angularindepth.com/everything-you-need-to-know-about-the-expressionchangedafterithasbeencheckederror-error-e3fd9ce7dbb4](https://blog.angularindepth.com/everything-you-need-to-know-about-the-expressionchangedafterithasbeencheckederror-error-e3fd9ce7dbb4)
