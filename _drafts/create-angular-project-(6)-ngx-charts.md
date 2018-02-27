---
layout: post
title: Create Angular v2+ project (6) - ngx-charts
author: Andy Feng
---

# Overview #
Data visualization a very helpful to present data in a straightfoward way. As front-end technologies expand nowdays, dozens of Chart frameworks have been developed. Here's a recent list we compiled during our effort to open-source this:+

- [ngx-charts](https://github.com/swimlane/ngx-charts) open source Angular2+ chart framework with native components and great extensibility
- [ng2-nvd3](https://github.com/krispo/ng2-nvd3) Open-source wrapper for nvd3 using Angular2 Components
- [ng2-charts](http://valor-software.com/ng2-charts/) Open-source Angular2 wrapper directives for Chart.js
- [angular2-highcharts](https://www.npmjs.com/package/angular2-highcharts) Open-source Angular2 wrapper for HighCharts
- [recharts](http://recharts.org/) Open-source composable charting library built on React components
- [Vega](http://vega.github.io/) Open-source Canvas/SVG viz framework
- [C3](http://c3js.org/) Open-source D3-based reusable chart library
- [Plotly](https://plot.ly/) Commercial business intelligence and data science.
- [Highcharts](http://www.highcharts.com/) Commercial chart framework
- [eCharts](http://echarts.baidu.com/demo.htm) Open-source JavaScript chart framework
- [dc.js](http://dc-js.github.io/dc.js) - Open-source JavaScript charting library

# Introduction #
[ngx-charts](https://github.com/swimlane/ngx-charts) (formerly ng2d3) is a powerful chart library based on d3.js. It uses Angular to render and animate the [SVG](https://www.w3schools.com/html/html5_svg.asp) elements for visualizing data via charts. It uses MIT license and is open for commercial purpose.

![](/images/posts/20180208-ngx-chart-1.png)

Major features include:

- Native Angular component instead of a simple wrapper
- Angular click events on every chart element possible
- Separation of shared & common chart elements
- Easily add new chart components in a modular fashion
- Everything is styled via SCSS

It contains a set of charts and can meet most of charting requirements:

- Horizontal & Vertical Bar Charts (Standard, Grouped, Stacked, Normalized)
- Line
- Area (Standard, Stacked, Normalized)
- Pie (Explodable, Grid, Custom legends)
- Bubble
- Donut
- Gauge (Linear & Radial)
- Force Directed Graphan
- Heatmap
- Treemap
- Number Cards
- and more

Besides above pre-made charts, ngx-charts also exports all of the components and helpers used as building blocks for charts. Things like legends, axes, dimension helpers, gradients, shapes, series of shapes, can all be directly imported into our application and used in our own components. This allows us to build custom charts.

# Installation #
1. create a typical Angular 2+ project via cli or other approaches

	`ng new my-charts`

1. install ngx-charts via npm

	`npm install @swimlane/ngx-charts --save`
	`npm install d3 --save`
	`npm install @types/d3 --save-dev`

1. import css styles, modify .angular-cli.json

	```
	...
	"styles": [
	   "styles.css",
	   "../node_modules/@swimlane/ngx-charts/release/ngx-charts.css"
	],
	...
	```

1. import NgxChartsModule. Modify app.module.ts

	```	
	@NgModule({
	  declarations: [
	    ...
	  ],
	  imports: [
	    ...
	    NgxChartsModule
	  ],
	  exports: [],
	  bootstrap: [AppComponent]
	 })
	export class AppModule {
	}
	```

# Create a component #
1. Create a new component: `my-chart.component.ts`

		import {Component} from '@angular/core';
		import {single} from './data';
		
		@Component({
		    selector: 'my-chart',
		    template: `
		      <ngx-charts-bar-vertical
		        [view]="view"
		        [scheme]="colorScheme"
		        [results]="single"
		        [gradient]="gradient"
		        [xAxis]="showXAxis"
		        [yAxis]="showYAxis"
		        [legend]="showLegend"
		        [showXAxisLabel]="showXAxisLabel"
		        [showYAxisLabel]="showYAxisLabel"
		        [xAxisLabel]="xAxisLabel"
		        [yAxisLabel]="yAxisLabel"
		        (select)="onSelect($event)">
		      </ngx-charts-bar-vertical>
		    `
		  })
		  export class MyChartComponent {
		    single: any[];
		    multi: any[];
		  
		    view: any[] = [700, 400];
		  
		    // options
		    showXAxis = true;
		    showYAxis = true;
		    gradient = false;
		    showLegend = true;
		    showXAxisLabel = true;
		    xAxisLabel = 'Country';
		    showYAxisLabel = true;
		    yAxisLabel = 'Population';
		  
		    colorScheme = {
		      domain: ['#5AA454', '#A10A28', '#C7B42C', '#AAAAAA']
		    };
		  
		    constructor() {
		      Object.assign(this, {single})   
		    }
		    
		    onSelect(event) {
		      console.log(event);
		    }
		  }  

1. create `data.ts`

		export let single: any = [
		    {
		      'name': 'Databases',
		      'value': 382941,
		    },
		    {
		      'name': 'Containers',
		      'value': 152294,
		    },
		    {
		      'name': 'Streams',
		      'value': 283000,
		    },
		    {
		      'name': 'Queries',
		      'value': 828921,
		    },
		  ];
		  
		  export let multi: any = [
		    {
		      'name': 'Databases',
		      'series': [
		        {
		          'value': 2469,
		          'name': '2016-09-15T19:25:07.773Z',
		        },
		        {
		          'value': 3619,
		          'name': '2016-09-17T17:16:53.279Z',
		        },
		        {
		          'value': 3885,
		          'name': '2016-09-15T10:34:32.344Z',
		        },
		        {
		          'value': 4289,
		          'name': '2016-09-19T14:33:45.710Z',
		        },
		        {
		          'value': 3309,
		          'name': '2016-09-12T18:48:58.925Z',
		        },
		      ],
		    },
		    {
		      'name': 'Containers',
		      'series': [
		        {
		          'value': 2452,
		          'name': '2016-09-15T19:25:07.773Z',
		        },
		        {
		          'value': 4938,
		          'name': '2016-09-17T17:16:53.279Z',
		        },
		        {
		          'value': 4110,
		          'name': '2016-09-15T10:34:32.344Z',
		        },
		        {
		          'value': 3828,
		          'name': '2016-09-19T14:33:45.710Z',
		        },
		        {
		          'value': 5772,
		          'name': '2016-09-12T18:48:58.925Z',
		        },
		      ],
		    },
		    {
		      'name': 'Streams',
		      'series': [
		        {
		          'value': 4022,
		          'name': '2016-09-15T19:25:07.773Z',
		        },
		        {
		          'value': 2345,
		          'name': '2016-09-17T17:16:53.279Z',
		        },
		        {
		          'value': 5148,
		          'name': '2016-09-15T10:34:32.344Z',
		        },
		        {
		          'value': 6868,
		          'name': '2016-09-19T14:33:45.710Z',
		        },
		        {
		          'value': 5415,
		          'name': '2016-09-12T18:48:58.925Z',
		        },
		      ],
		    },
		    {
		      'name': 'Queries',
		      'series': [
		        {
		          'value': 6194,
		          'name': '2016-09-15T19:25:07.773Z',
		        },
		        {
		          'value': 6585,
		          'name': '2016-09-17T17:16:53.279Z',
		        },
		        {
		          'value': 6857,
		          'name': '2016-09-15T10:34:32.344Z',
		        },
		        {
		          'value': 2545,
		          'name': '2016-09-19T14:33:45.710Z',
		        },
		        {
		          'value': 5986,
		          'name': '2016-09-12T18:48:58.925Z',
		        },
		      ],
		    },
		  ];

1. declare this component in app.module.ts

		import { MyChartComponent } from './components/my-chart.component';

		@NgModule({
		  declarations: [
			MyChartComponent,
			...


1. `ng start `

	![](/images/posts/20180208-ngx-chart-3.png)

# Create a component with Covalent #
1. Install covalent library, import theme. Please check previous posting for details.

1. Create a new component: `ngx-charts.component.ts`

		import { Component } from '@angular/core';
		import { single, multi } from './data';
		
		import { TdDigitsPipe } from '@covalent/core/common';
		
		@Component({
		  selector: 'ngx-charts-demo',
		  styleUrls: [],
		  templateUrl: './ngx-charts.component.html'
		})
		export class NgxChartsDemoComponent {
		
		  // Chart
		  single: any[];
		  multi: any[];
		
		  view: any[] = [700, 400];
		
		  // options
		  showXAxis: boolean = true;
		  showYAxis: boolean = true;
		  gradient: boolean = true;
		  showLegend: boolean = false;
		  showXAxisLabel: boolean = true;
		  xAxisLabel: string = '';
		  showYAxisLabel: boolean = true;
		  yAxisLabel: string = 'Sales';
		
		  colorScheme: any = {
		    domain: ['#1565C0', '#03A9F4', '#FFA726', '#FFCC80'],
		  };
		
		  // line, area
		  autoScale: boolean = true;
		
		  constructor() {
		    // Chart Single
		    Object.assign(this, {single});
		    // Chart Multi
		    this.multi = multi.map((group: any) => {
		      group.series = group.series.map((dataItem: any) => {
		        dataItem.name = new Date(dataItem.name);
		        return dataItem;
		      });
		      return group;
		    });
		  }
		
		  // ngx transform using covalent digits pipe
		  axisDigits(val: any): any {
		    return new TdDigitsPipe().transform(val);
		  }
		}

1. Create template `ngx-charts.component.html`

		<div>
	        <div layout="row" class="pull-bottom pull-right">
	          <ngx-charts-number-card
	              [scheme]="colorScheme"
	              [results]="single">
	            </ngx-charts-number-card>
	        </div>
	        <div layout-gt-xs="row">
	          <div flex-gt-xs="50">
	            <mat-card>
	              <mat-card-title>Gauge</mat-card-title>
	              <mat-divider></mat-divider>
	              <div style="height:250px;">
	                <ngx-charts-gauge
	                  [scheme]="colorScheme"
	                  [results]="single"
	                  [min]="0"
	                  [max]="100"
	                  [units]="'usage'"
	                  [bigSegments]="10"
	                  [smallSegments]="5"
	                  [valueFormatting]="axisDigits">
	                </ngx-charts-gauge>
	              </div>
	            </mat-card>
	          </div>
	          <div flex-gt-xs="50">
	            <mat-card>
	              <mat-card-title>Stacked Horizontal Bars</mat-card-title>
	              <mat-divider></mat-divider>
	              <div style="height:250px;">
	                <ngx-charts-bar-horizontal-stacked
	                  [scheme]="colorScheme"
	                  [results]="multi"
	                  [gradient]="gradient"
	                  [xAxis]="showXAxis"
	                  [yAxis]="showYAxis"
	                  [legend]="showLegend"
	                  [showXAxisLabel]="showXAxisLabel"
	                  [showYAxisLabel]="showYAxisLabel"
	                  [xAxisLabel]="xAxisLabel"
	                  [xAxisTickFormatting]="axisDigits">
	                </ngx-charts-bar-horizontal-stacked>
	              </div>
	            </mat-card>
	          </div>
	        </div>
	        <div layout-gt-xs="row">
	          <div flex-gt-xs="50">
	            <mat-card>
	              <mat-card-title>Vertical Grouped Bars</mat-card-title>
	              <mat-divider></mat-divider>
	              <div style="height:250px;">
	                <ngx-charts-bar-vertical-2d
	                  [scheme]="colorScheme"
	                  [results]="multi"
	                  [gradient]="gradient"
	                  [xAxis]="showXAxis"
	                  [yAxis]="showYAxis"
	                  [legend]="showLegend"
	                  [showXAxisLabel]="showXAxisLabel"
	                  [showYAxisLabel]="showYAxisLabel"
	                  [xAxisLabel]="xAxisLabel"
	                  [yAxisLabel]="yAxisLabel"
	                  [yAxisTickFormatting]="axisDigits"
	                  [barPadding]="2"
	                  [groupPadding]="8">
	                </ngx-charts-bar-vertical-2d>
	              </div>
	            </mat-card>
	          </div>
	          <div flex-gt-xs="50">
	            <mat-card>
	              <mat-card-title>Lines</mat-card-title>
	              <mat-divider></mat-divider>
	              <div style="height:250px;">
	                <ngx-charts-line-chart
	                  [scheme]="colorScheme"
	                  [results]="multi"
	                  [gradient]="gradient"
	                  [xAxis]="showXAxis"
	                  [yAxis]="showYAxis"
	                  [legend]="showLegend"
	                  [showXAxisLabel]="showXAxisLabel"
	                  [showYAxisLabel]="showYAxisLabel"
	                  [xAxisLabel]="xAxisLabel"
	                  [yAxisLabel]="yAxisLabel"
	                  [autoScale]="autoScale"
	                  [yAxisTickFormatting]="axisDigits">
	                </ngx-charts-line-chart>
	              </div>
	            </mat-card>
	          </div>
	        </div>
	      </div>

1. declare this component in app.module.ts


		import { NgxChartsDemoComponent } from './components/ngx-charts.component';

		@NgModule({
		  declarations: [
			NgxChartsDemoComponent
			...

1. `ng start `

	![](/images/posts/20180208-ngx-chart-2.png)

# References #

[Charts with Angular: ngx-charts](https://www.beyondjava.net/blog/charts-with-angular-ngx-charts-formerly-ng2d3/)