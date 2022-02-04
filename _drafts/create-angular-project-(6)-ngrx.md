---
layout: post
title: Create Angular v2+ project (6) - NGRX
author: Andy Feng
---

# Introduction #
NgRx is inspired by Redux and it provides Reactive State for Angular.

# Steps
1. Install

	`npm install @ngrx/store --save`
	
	or 
	
	`npm install @ngrx/store@9.2.0 --save`

1. modify app.module.ts

		import { StoreModule } from '@ngrx/store';
		...
		@NgModule({
		  imports: [
		    ...
		    StoreModule.forRoot({}) // StoreModule.forRoot({name1: reducer1})
		  ],

1. 设计state。我们需要增加一个toggle product code的功能开关，默认关闭，不显示product code；打开时显示product code

	另外，按feature组织state，避免一个大flat的store。所以用多个reducer来组织，每个reducer小且focus，类似下图，每个子state叫做slice

	![](/images/posts/20220120-react-13.jpg)

	> 使用feature组织big store，需要enable feaure module state composition

		e.g. app.module.ts

		StoreModule.forFeature('productCodes', productCodeReducer); // feature name 常用复数形式

	> 如果需要进一步分解state成sub slice, 参考下图

	![](/images/posts/20220120-react-14.jpg)

1. 创建reducer

		import { createAction, createReducer, on } from "@ngrx/store";
		
		export const productCodeReducer = createReducer(
		    { productCodeShow: true },
		    on(createAction('SHOW_PRODUCT_CODE'), state => {
		        console.log('original state' + JSON.stringify(state));
		        return {
		            ...state,
		            produceCodeShow: !state.productCodeShow
		        }
		    })
		)

1. 修改component从store读取数据。修改action event handler，调用dispatch action；修改其他需要相应action的component，subscribe store

		<input class="form-check-input"
                 type="checkbox"
                 (change)="checkChanged()"
                 [checked]="productCodeShow">
          Display Product Code
        </label>

		import { Store } from '@ngrx/store';
		...
		@Component({
		  ...
		})
		export class ProductListComponent implements OnInit, OnDestroy {
		  constructor(private productService: ProductService
		    , private store: Store<any>) {
		  }
		
		  ngOnInit(): void {
		    ...
			// subscribe the state change
		    this.store.select('productCodes').subscribe(
		      state => {
		        this.productCodeShow = state.productCodeShow;
		      }
		    )
		  }
		  // action event handler
		  checkChanged(): void {
		    this.store.dispatch({ type: 'SHOW_PRODUCT_CODE' })
		  }
		}

1. enable redux devtools
	benefits:
	- inspect action logs
	- visualize state tree
	- time travel debugging

	chrome/firefox > install redux devtools

	![](/images/posts/20220120-react-15.jpg)

	`npm install @ngrx/store-devtools --save`

	Modify app.js to initialize @ngrx/store-devtools module
		
		import { StoreDevtoolsModule } from '@ngrx/store-devtools';
		import { environment } from 'src/environments/environment';
		...
		@NgModule({
		  imports: [
		    ...
		    StoreModule.forRoot({}),
		    StoreDevtoolsModule.instrument({
		      name: 'PRODUCT DEMO',
		      maxAge: 25,
		      logOnly: environment.production
		    })
		  ],
	
	make some actions > redux tab

	![](/images/posts/20220120-react-16.jpg)
# References
[ngrx.io](https://ngrx.io/)