---
layout: post
title: Create Angular v2+ project (8) - Component Communication
author: Andy Feng
---

# Pass data
[Download source](/download/202102-pass-data-from-parent-to-child.zip)

If we have 2 components and they share the same model object which parent component pass the model object to child component:

## way1: 
parent component

	@Component({
	    selector: 'a-comp',
	    template: `
	        <header>I am header of parent component </header>
	        <b-comp [model]="model"></b-comp>
	    `
	})
	export class AComponent implements OnInit {
	    model : Model = new Model();

		constructor() {}
	
	    ngOnInit() {
	    }
	}

child component:

	@Component({
	    selector: 'b-comp',
	    template: `
			<p>I am text in child component</p>
	    `
	})
	export class BComponent implements OnInit {
	    private _model = new Model();
	
	    @Input() set model(val: Model) {
	        this._model = val;
	    }
	    get model() {	        
	        return this._model;
	    }

	    constructor() {}
	
	    ngOnInit() {
			this._model.To = new Date(); // might cause issue, move to ngOninit() to resolve
	    }
	}

model class:

	export class Model {
	    public Id : number;
	    public Name: string;
	    public From: Date;
	    public To: Date;
	    public Comment : string;
	}

1. 其中，parent component A 直接将model 传递给 child component B, 在B中对model的任何改变，将直接反应到A

1. 但B中的 ngOnInit() 对model的改变，在dev环境将引发异常 ExpressionChangedAfterItHasBeenCheckedError: Expression has changed after it was checked. 

	a solution is move the `this.model.To = new Date();` to Model object itself

## way 2:
parent component

	@Component({
	    selector: 'a-comp',
	    template: `
	        <header>I am header of parent component </header>
	        <b-comp [model]="model" (toChanged)="model.To = $event"></b-comp>
	    `
	})
	export class AComponent implements OnInit {
	    model : Model = new Model();

		constructor() {}
	
	    ngOnInit() {
	    }
	}

child component:

	@Component({
	    selector: 'b-comp',
	    template: `
			<p>I am text in child component</p>
	    `
	})
	export class BComponent implements OnInit, AfterContentInit, AfterContentChecked {
	    private _model = new Model();
	    @Output() toChanged: EventEmitter<Date> = new EventEmitter<Date>();
	
	    set model(val: Model) {
	        console.log('set model in b');
	        this._model = new Model(val);
	    }
	    get model() {
	        console.log('get model in b');
	        return this._model;
	    }
	
	    constructor() {
	    }
	
	    ngAfterContentInit(): void {
	    }
	
	    ngAfterContentChecked(): void {
	    }
	
	    ngOnInit() {
	    }
	
	    changeTo(val : Date){
	        this._model.To = val
	        this.toChanged.emit(new Date(2021, 1, 1));
	    }
	
	}

any changes in child component trigger an event and parent component catch this event and make changes accordingly.

# References
