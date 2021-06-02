---
layout: post
title: Create Angular v2+ project (15) - Fileupload
author: Andy Feng
---

# Introduction #
# ng2-pdf-viewer 
	
	[https://www.npmjs.com/package/ng2-pdf-viewer](https://www.npmjs.com/package/ng2-pdf-viewer)


Please note:

1. looks like some compatility issue with ie 11

	`SCRIPT1010: Expected identifier`
	 
# ngx-extended-pdf-viewer

[https://www.npmjs.com/package/ngx-extended-pdf-viewer](https://www.npmjs.com/package/ngx-extended-pdf-viewer)

1. install 

	npm i ngx-extended-pdf-viewer --save

1. app.module.ts

		...
		import { NgxExtendedPdfViewerModule } from 'ngx-extended-pdf-viewer';
		
		@NgModule({
		  imports: [
		    NgxExtendedPdfViewerModule,
		    ....
		
		  ],
		  declarations: [
		    AppComponent,
		    ...
		
		
		  ],
		  providers: [
		   ...
		  ],
		  bootstrap: [AppComponent]
		})
		export class AppModule { }


1. angular.json (or .angular-cli.json if you're using an older version of Angular)

  "assets": [
    "src/favicon.ico",
    "src/assets",
	{...},
    {
      "glob": "**/*",
      "input": "node_modules/ngx-extended-pdf-viewer/assets/",
      "output": "/assets/"
    }
  ],
  "scripts": []


1. display the PDF file like so:

	<ngx-extended-pdf-viewer
	     src="some.pdf"
	     useBrowserLocale="true">
	</ngx-extended-pdf-viewer>

please note:
1. cannot load pdf and shows " Getting “net::ERR_BLOCKED_BY_CLIENT” error on some AJAX calls" errors
	disable chrome's adblocker extension
	disalbe IDM(Internet Download Manager) extension
	
1. cannot display in IE v11

	![](/images/posts/20200922-angular-2.png)

	solution:

	![](/images/posts/20200922-angular-1.png)

# References
