---
layout: post
title: Create Angular v2+ project (9) - ng2-select
author: Andy Feng
---

# Installation #
1. download soure code at [https://github.com/valor-software/ng2-select](https://github.com/valor-software/ng2-select)

1. Install ng2-select package via npm

	npm install ng2-select --save

	![](/images/posts/20180410-ng2-select-1.png)

1. Include ng2-select.css in your project

	![](/images/posts/20180410-ng2-select-2.png)
	
	add css

		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
		<link rel="stylesheet" href="assets/css/ng2-select.css">

1. Add ngselect module

	app.module.ts

	import { Ng2Component } from './ng2/ng2.component';
	import { FormsModule } from '@angular/forms';

	import {SelectModule} from 'ng2-select';

# demo #
1. create a component `ng2.component.ts`

		import { Component, OnInit } from '@angular/core';	
		@Component({
		  selector: 'app-ng2',
		  templateUrl: './ng2.component.html',
		  styleUrls: ['./ng2.component.scss']
		})
		export class Ng2Component implements OnInit {	
		  constructor() { }
		
		  ngOnInit() {
		  }
		
		  public items: Array<string> = ['Amsterdam', 'Antwerp', 'Athens', 'Barcelona',
		    'Berlin', 'Birmingham', 'Bradford', 'Bremen', 'Brussels', 'Bucharest',
		    'Budapest', 'Cologne', 'Copenhagen', 'Dortmund', 'Dresden', 'Dublin', 'Düsseldorf',
		    'Essen', 'Frankfurt', 'Genoa', 'Glasgow', 'Gothenburg', 'Hamburg', 'Hannover',
		    'Helsinki', 'Leeds', 'Leipzig', 'Lisbon', 'Łódź', 'London', 'Kraków', 'Madrid',
		    'Málaga', 'Manchester', 'Marseille', 'Milan', 'Munich', 'Naples', 'Palermo',
		    'Paris', 'Poznań', 'Prague', 'Riga', 'Rome', 'Rotterdam', 'Seville', 'Sheffield',
		    'Sofia', 'Stockholm', 'Stuttgart', 'The Hague', 'Turin', 'Valencia', 'Vienna',
		    'Vilnius', 'Warsaw', 'Wrocław', 'Zagreb', 'Zaragoza'];
		
		  private value: any = ['Athens'];
		  private _disabledV: string = '0';
		  private disabled: boolean = false;
		
		  private get disabledV(): string {
		    return this._disabledV;
		  }
		
		  private set disabledV(value: string) {
		    this._disabledV = value;
		    this.disabled = this._disabledV === '1';
		  }
		
		  public selected(value: any): void {
		    console.log('Selected value is: ', value);
		  }
		
		  public removed(value: any): void {
		    console.log('Removed value is: ', value);
		  }
		
		  public refreshValue(value: any): void {
		    this.value = value;
		  }
		
		  public itemsToString(value: Array<any> = []): string {
		    return value
		      .map((item: any) => {
		        return item.text;
		      }).join(',');
		  }
		  public type(value : any) : void{
		    console.log('you typed value is ', value);
		  }
		}

1. create template `ng2.component.html`

		<div style="width: 300px; margin-bottom: 20px;">
		  <h3>Select multiple cities</h3>
		  <ng-select  [multiple]="true"
		              [items]="items"
		              [disabled]="disabled"
		              (data)="refreshValue($event)"
		              (selected)="selected($event)"
		              (removed)="removed($event)"
		              placeholder="No city selected"
					  (typed)="type($event)"></ng-select>
		  <pre>{{itemsToString(value)}}</pre>
		  <div>
		    <button type="button" class="btn btn-primary"
		            [(ngModel)]="disabledV" btnCheckbox
		            btnCheckboxTrue="1" btnCheckboxFalse="0">
		      {{disabled === '1' ? 'Enable' : 'Disable'}}
		    </button>
		  </div>
		</div>

1. add component `app.component.html`

		<app-ng2></app-ng2>

1. start the app, `ng server`

	![](/images/posts/20180410-ng2-select-4.png)
