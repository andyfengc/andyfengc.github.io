---
layout: post
title: Create Angular v2+ project (2) - Kendo UI
author: Andy Feng
---

## Introduction ##
Kendo UI is a complete JavaScript UI component library that allows us to quickly build web applications. Kendo UI has two versions:

- Kendo UI Core is the free and open-source subset of Kendo UI. It is essential for developing great experiences for the web and mobile.
- Kendo UI is the commercial version. It includes 70+ widgets and help us build applications quicker and easier.

## Outline ##
1. Prepare Angular 2+ project
2. Add kendo grid package 
3. Add more components

## Prepare Angular 2+ project ##
Here, we use [Angular CLI](https://github.com/angular/angular-cli) to build an empty project. 

	npm install -g @angular/cli 
	ng new my-first-angular-project --style=scss
	cd my-first-angular-project

We can also use existing Angular project

Since the Angular project is ready, we need to add Kendo packages into our own components. Kendo UI for Angular is distributed as multiple NPM packages, scoped to @progress. For example, the name of the Grid package is @progress/kendo-angular-grid. 

## Add kendo grid package ##

1. First, we have to install the right package, here is kendo-angular-grid package to our Angular project.

		npm install --save @progress/kendo-angular-grid @progress/kendo-angular-dropdowns @progress/kendo-angular-inputs @progress/kendo-angular-dateinputs @progress/kendo-data-query @progress/kendo-angular-intl @progress/kendo-angular-l10n @progress/kendo-drawing @progress/kendo-angular-excel-export @angular/animations

	Please note that Kendo UI components use `Angular animations`. When it comes to Angular release, we have to install the `@angular/animations` package.

1. Next, import the component directives - GridModule into our application root module. Here, update app.module.ts:

	import { BrowserModule } from '@angular/platform-browser';
	import { NgModule } from '@angular/core';
	import { AppComponent } from './app.component';
	import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
	import { GridModule } from '@progress/kendo-angular-grid';
	
	@NgModule({
	  declarations: [
	    AppComponent
	  ],
	  imports: [
	    BrowserModule,
	    // Register the modules
	    BrowserAnimationsModule,
	    GridModule
	  ],
	  providers: [],
	  bootstrap: [AppComponent]
	})
	export class AppModule { }


1. Update the component to add grid component.

	template page: app.component.html

		<h1>{{title}}</h1>
		
		<kendo-grid [data]="gridData" [height]="410">
		  <kendo-grid-column field="ProductID" title="ID" width="40">
		  </kendo-grid-column>
		  <kendo-grid-column field="ProductName" title="Name" width="250">
		  </kendo-grid-column>
		  <kendo-grid-column field="Category.CategoryName" title="Category">
		  </kendo-grid-column>
		  <kendo-grid-column field="UnitPrice" title="Price" width="80">
		  </kendo-grid-column>
		  <kendo-grid-column field="UnitsInStock" title="In stock" width="80">
		  </kendo-grid-column>
		  <kendo-grid-column field="Discontinued" title="Discontinued" width="120">
		      <ng-template kendoGridCellTemplate let-dataItem>
		          <input type="checkbox" [checked]="dataItem.Discontinued" disabled/>
		      </ng-template>
		  </kendo-grid-column>
		</kendo-grid>

	component app.component.ts:

		import { Component } from '@angular/core';
		import { products } from './products';
		@Component({
		  selector: 'app-root',
		  templateUrl: './app.component.html',
		  styleUrls: ['./app.component.scss']
		})
		export class AppComponent {
		  title = 'Hello World!';
		  public gridData: any[] = products;
		}

	data products.ts: 

		export const products = [{
		    "ProductID": 1,
		    "ProductName": "Chai",
		    "SupplierID": 1,
		    "CategoryID": 1,
		    "QuantityPerUnit": "10 boxes x 20 bags",
		    "UnitPrice": 18.0000,
		    "UnitsInStock": 39,
		    "UnitsOnOrder": 0,
		    "ReorderLevel": 10,
		    "Discontinued": false,
		    "Category": {
		        "CategoryID": 1,
		        "CategoryName": "Beverages",
		        "Description": "Soft drinks, coffees, teas, beers, and ales"
		    }
		}, {
		    "ProductID": 2,
		    "ProductName": "Chang",
		    "SupplierID": 1,
		    "CategoryID": 1,
		    "QuantityPerUnit": "24 - 12 oz bottles",
		    "UnitPrice": 19.0000,
		    "UnitsInStock": 17,
		    "UnitsOnOrder": 40,
		    "ReorderLevel": 25,
		    "Discontinued": false,
		    "Category": {
		        "CategoryID": 1,
		        "CategoryName": "Beverages",
		        "Description": "Soft drinks, coffees, teas, beers, and ales"
		    }
		}, {
		    "ProductID": 3,
		    "ProductName": "Aniseed Syrup",
		    "SupplierID": 1,
		    "CategoryID": 2,
		    "QuantityPerUnit": "12 - 550 ml bottles",
		    "UnitPrice": 10.0000,
		    "UnitsInStock": 13,
		    "UnitsOnOrder": 70,
		    "ReorderLevel": 25,
		    "Discontinued": false,
		    "Category": {
		        "CategoryID": 2,
		        "CategoryName": "Condiments",
		        "Description": "Sweet and savory sauces, relishes, spreads, and seasonings"
		    }
		}, {
		    "ProductID": 4,
		    "ProductName": "Chef Anton's Cajun Seasoning",
		    "SupplierID": 2,
		    "CategoryID": 2,
		    "QuantityPerUnit": "48 - 6 oz jars",
		    "UnitPrice": 22.0000,
		    "UnitsInStock": 53,
		    "UnitsOnOrder": 0,
		    "ReorderLevel": 0,
		    "Discontinued": false,
		    "Category": {
		        "CategoryID": 2,
		        "CategoryName": "Condiments",
		        "Description": "Sweet and savory sauces, relishes, spreads, and seasonings"
		    }
		}, {
		    "ProductID": 5,
		    "ProductName": "Chef Anton's Gumbo Mix",
		    "SupplierID": 2,
		    "CategoryID": 2,
		    "QuantityPerUnit": "36 boxes",
		    "UnitPrice": 21.3500,
		    "UnitsInStock": 0,
		    "UnitsOnOrder": 0,
		    "ReorderLevel": 0,
		    "Discontinued": true,
		    "Category": {
		        "CategoryID": 2,
		        "CategoryName": "Condiments",
		        "Description": "Sweet and savory sauces, relishes, spreads, and seasonings"
		    }
		}
		];

1. Add styles

	Please note that the Kendo UI for Angular themes are distributed as separate NPM packages. At the time, the available theme packages are @progress/kendo-theme-default and @progress/kendo-theme-bootstrap.

	Here, install the default theme: `npm install --save @progress/kendo-theme-default`

	Now, import the SCSS file from the package in src/styles.scss:

		@import "~@progress/kendo-theme-default/scss/all";

1. Run the application

	`ng server --port 5002`

	![](/images/posts/20180108-angular2-kendo-1.png)

1. Add sync pagination

	way1: use [kendoGridBinding] attribute

	update app.component.html

		<kendo-grid [kendoGridBinding]="gridData"
		    [height]="600"
		    [pageSize]="pageSize"
		    [pageable]="true"
		    [sortable]="true"
		    [filterable]="true"
		    [groupable]="true"
		>
		    <kendo-grid-column field="ProductID" title="ID" width="40">
		    </kendo-grid-column>
		    <kendo-grid-column field="ProductName" title="Name" width="250">
		    </kendo-grid-column>
		    <kendo-grid-column field="Category.CategoryName" title="Category">
		    </kendo-grid-column>
		    <kendo-grid-column field="UnitPrice" title="Price" width="80">
		    </kendo-grid-column>
		    <kendo-grid-column field="UnitsInStock" title="In stock" width="80">
		    </kendo-grid-column>
		    <kendo-grid-column field="Discontinued" title="Discontinued" width="120">
		        <ng-template kendoGridCellTemplate let-dataItem>
		            <input type="checkbox" [checked]="dataItem.Discontinued" disabled/>
		        </ng-template>
		    </kendo-grid-column>
		</kendo-grid>

	update app.component.html

		...
		import { products } from './products';
		...
		export class AppComponent {
		  public pageSize = 2;
		  private gridData: any[] = products;
		
		  constructor(private service: CategoriesService) {
		  }
		}

	way2: Paging is enabled through the pageable, pageSize, and skip options of the Grid. Also, each paging action triggers an event and we can handle the pageChange event that is emitted ourselves. Sorting action triggers sort event as well.

	update app.component.html

		<kendo-grid [data]="gridView" [height]="410"
		[pageSize]="pageSize"
		[skip]="skip"
		[pageable]="true"
		(dataStateChange)="changePage($event)"
		>
		    <kendo-grid-column field="ProductID" title="ID" width="40">
		    </kendo-grid-column>
		    <kendo-grid-column field="ProductName" title="Name" width="250">
		    </kendo-grid-column>
		    <kendo-grid-column field="Category.CategoryName" title="Category">
		    </kendo-grid-column>
		    <kendo-grid-column field="UnitPrice" title="Price" width="80">
		    </kendo-grid-column>
		    <kendo-grid-column field="UnitsInStock" title="In stock" width="80">
		    </kendo-grid-column>
		    <kendo-grid-column field="Discontinued" title="Discontinued" width="120">
		        <ng-template kendoGridCellTemplate let-dataItem>
		            <input type="checkbox" [checked]="dataItem.Discontinued" disabled/>
		        </ng-template>
		    </kendo-grid-column>
		</kendo-grid>

	update app.component.ts

		import {
		    GridDataResult,
		    PageChangeEvent 
		} from '@progress/kendo-angular-grid';
		...
		export class AppComponent {
		  public gridView: GridDataResult;
		  public skip = 0;
		  public pageSize = 2;
		  private items: any[] = products;
		
		  constructor(private service: CategoriesService) {
		      this.loadItems();
		  }
		
		  public changePage(event: PageChangeEvent): void {
		    this.skip = event.skip;
		    this.loadItems();
		    }
		
		  private loadItems(): void {
		    this.gridView = {
		      data: this.items.slice(this.skip, this.skip + this.pageSize),
		      total: this.items.length
		    };
		  }
		}

	run the application via `npm serve --port 5002`

	![](/images/posts/20180108-angular2-kendo-2.png)
	
1. Add async pagination

	install @progress/kendo-data-query lib

		npm install --save @progress/kendo-data-query

	add a new service to access data asynchoronously app.service.ts

		import { Injectable } from '@angular/core';
		import { HttpClient } from '@angular/common/http';
		import { GridDataResult } from '@progress/kendo-angular-grid';
		import { toODataString } from '@progress/kendo-data-query';
		import { Observable } from 'rxjs/Observable';
		import { BehaviorSubject } from 'rxjs/BehaviorSubject';
		
		import 'rxjs/add/operator/map';
		
		export abstract class NorthwindService extends BehaviorSubject<GridDataResult> {
		    private BASE_URL = 'https://odatasampleservices.azurewebsites.net/V4/Northwind/Northwind.svc/';
		
		    constructor(
		        private http: HttpClient,
		        protected tableName: string
		    ) {
		        super(null);
		    }
		
		    public query(state: any): void {
		        this.fetch(this.tableName, state)
		            .subscribe(x => super.next(x));
		    }
		
		    protected fetch(tableName: string, state: any): Observable<GridDataResult> {
		        const queryStr = `${toODataString(state)}&$count=true`;
		
		        return this.http
		            .get(`${this.BASE_URL}${tableName}?${queryStr}`)
		            .map(response => (<GridDataResult>{
		                data: response['value'],
		                total: parseInt(response['@odata.count'], 10)
		            }));
		    }
		}
		
		@Injectable()
		export class CategoriesService extends NorthwindService {
		    constructor(http: HttpClient) { super(http, 'Categories'); }
		
		    queryAll(st?: any): Observable<GridDataResult> {
		      const state = Object.assign({}, st);
		      delete state.skip;
		      delete state.take;
		
		      return this.fetch(this.tableName, state);
		    }
		}

	update app.module.ts to import this service

		import { HttpClientModule } from '@angular/common/http';
		import { CategoriesService } from './app.service';
		@NgModule({
		  ...
		  imports: [
		   	...
		    HttpClientModule 
		  ],
		  providers: [CategoriesService ],
		  bootstrap: [AppComponent]
		})
		export class AppModule { }

	Update app.component.ts

		import { Observable } from 'rxjs/Rx';
		import { CategoriesService } from './app.service';
		import { State } from '@progress/kendo-data-query';
		...
		import {
		  GridDataResult,
		  DataStateChangeEvent
		} from '@progress/kendo-angular-grid';
		
		@Component({
		  selector: 'app-root',
		  templateUrl: './app.component.html',
		  styleUrls: ['./app.component.scss']
		})
		export class AppComponent {
		  title = 'Hello World!';
		  public view: Observable<GridDataResult>;
		  public state: State = {
		    skip: 0,
		    take: 5
		  };
		
		  constructor(private service: CategoriesService) {
		    this.view = service;
		    this.service.query(this.state);
		  }
		
		  public dataStateChange(state: DataStateChangeEvent): void {
		    this.state = state;
		    this.service.query(state);
		  }
		}

	Update app.component.html

		<kendo-grid
		[data]="view | async"
		[pageSize]="state.take"
		[skip]="state.skip"
		[sort]="state.sort"
		[sortable]="true"
		[pageable]="true"
		[scrollable]="'none'"
		(dataStateChange)="dataStateChange($event)"
		>
		<kendo-grid-column field="CategoryID" width="130"></kendo-grid-column>
		<kendo-grid-column field="CategoryName" width="200"></kendo-grid-column>
		<kendo-grid-column field="Description" [sortable]="false">
		</kendo-grid-column>
		</kendo-grid>

	Run the application: `ng serve`

	![](/images/posts/20180108-angular2-kendo-3.png)

1. Add PDF export support

	import PDFModule in app.module.ts

		import { GridModule, PDFModule } from '@progress/kendo-angular-grid';
		@NgModule({
		  declarations: [
		    ...
		  ],
		  imports: [    
			...
		    GridModule, 
		    PDFModule
		  ],
			...
		}

	Update app.component.html

		<kendo-grid 
			[kendoGridBinding]="gridData"
		    [height]="600"
		    [pageSize]="pageSize"
		    [pageable]="true"
		    [sortable]="true"
		    [filterable]="true"
		    [groupable]="true"
		        >
		
		        <ng-template kendoGridToolbarTemplate>
		            <button kendoGridPDFCommand><span class='k-icon k-i-file-pdf'></span>Export to PDF</button>
		        </ng-template>
		
			    <kendo-grid-column...>
			    	...
			    </kendo-grid-column>
			    ...
			
			    <kendo-grid-pdf fileName="data.pdf" [allPages]="true">
			        <kendo-grid-pdf-margin top="1cm" left="1cm" right="1cm" bottom="1cm"></kendo-grid-pdf-margin>
			    </kendo-grid-pdf>
		
		  </kendo-grid>

	Update app.component.ts

		...
		import { products } from './products';
		import { GridModule, PDFModule } from '@progress/kendo-angular-grid';
		...
		export class SampleReportViewComponent {
			public pageSize = 2;
			private gridData: any[] = products;	
			...
		}

1. Add Excel export support

	import ExcelModule in app.module.ts

		import { GridModule, PDFModule, ExcelModule } from '@progress/kendo-angular-grid';

		@NgModule({
		  declarations: [
		    ...
		  ],
		  imports: [    
			...
		    GridModule, 
		    PDFModule,
    		ExcelModule,
		  ],
			...
		}

	Update app.component.html. Please note that by default, the Grid exports its current data. To export data that is different from the current Grid data, specify a custom [fetchData] function. It returns an ExcelExportData value or array.

		<kendo-grid 
			[kendoGridBinding]="gridData"
		    [height]="600"
		    [pageSize]="pageSize"
		    [pageable]="true"
		    [sortable]="true"
		    [filterable]="true"
		    [groupable]="true"
		        >
		
		        <ng-template kendoGridToolbarTemplate>
		            <button kendoGridPDFCommand><span class='k-icon k-i-file-pdf'></span>Export to PDF</button>
          			<button type="button" kendoGridExcelCommand ><span class="k-icon k-i-file-excel"></span>Export to Excel</button>
		        </ng-template>
		
			    <kendo-grid-column...>
			    	...
			    </kendo-grid-column>
			    ...
			
			    <kendo-grid-pdf fileName="data.pdf" [allPages]="true">
			        <kendo-grid-pdf-margin top="1cm" left="1cm" right="1cm" bottom="1cm"></kendo-grid-pdf-margin>
			    </kendo-grid-pdf>
				<kendo-grid-excel fileName="data.xlsx" [fetchData]="allData"></kendo-grid-excel>
		
		  </kendo-grid>

	Update app.component.ts. 

		...
		import { products } from './products';
		import { GridModule, PDFModule, ExcelModule  } from '@progress/kendo-angular-grid';
		import { ExcelExportData } from '@progress/kendo-angular-excel-export';
		import { process } from '@progress/kendo-data-query';
		...
		export class SampleReportViewComponent {
			public pageSize = 2;
			private gridData: any[] = products;	
	
			constructor() {
				this.allData = this.allData.bind(this);
			}

		    public allData(): ExcelExportData {
		        let self=this;
		        const result: ExcelExportData =  {
		            data: process(self.gridData, {}).data
		        };
		        return result;
		    }
		}

## Add more components ##
### Add date picker ###

## Add charts ##

1. Download and install the package:

	npm install --save @progress/kendo-angular-charts @progress/kendo-angular-intl @progress/kendo-angular-l10n @progress/kendo-drawing hammerjs @angular/animations

1. Import Hammer.js, ChartsModule
	
	app.module.ts

		// Imports the Chart module
		import { ChartsModule } from '@progress/kendo-angular-charts';
		import { ChartModule } from '@progress/kendo-angular-charts';
		import 'hammerjs';
		// Imports the Sparkline module
		import { SparklineModule } from '@progress/kendo-angular-charts';
		
		
		@NgModule({
		  ...
		  imports: [
		    ...
		    ChartsModule,
		    ChartModule, 
		    SparklineModule
		  ],
		  bootstrap: [AppComponent]
		})
		export class AppModule { }

1. Update component
	
	app.component.html

		<h2>Bar/Column Chart</h2>
		<kendo-sparkline [data]="data" type="pie" style="width: 300px; height: 200px;">
		</kendo-sparkline>

	app.component.ts

		export class AppComponent {
			public data : number[] = [1, 2, 3, 5];
			...
		}

1. result
	
	![](/images/posts/20180108-angular2-kendo-4.png)