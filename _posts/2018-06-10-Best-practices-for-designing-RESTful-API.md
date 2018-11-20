---
layout: post
title: Best practices for designing RESTful API
author: Andy Feng
categories: [api, restful]
---

## Naming convention ##
Avoid camel caps because urls are not case sentitive. e.g.

- api.service.com/helloWorld/userId/x
- api.service.com/helloworld/userid/x

are forwarded to same address.

The norm is to use lower case letters.

- api.service.com/helloworld/userid/x

If separate is required within urls, use dashes and avoid underscores:

- api.service.com/hello-world/user-id/x
 
## Use plural nouns ##

First of all, nouns are more easy understandable than verbs for each resource.

e.g. 

- /cars  
- /cars/123

Do not use verbs. Verbs are not intuitive to represent resources. e.g. (discouraged)

- /getAllCars
- /createNewCar
- /deleteAllRedCars

Furthermore, use plural nouns over singular ones. Given that the first thing most people do with a RESTful API with GET request, it is more intuitive to use plural nouns. e.g.

- GET /tickets - Retrieves a list of tickets

Same principle applied to other requests:

- GET /tickets/12 - Retrieves a specific ticket
- POST /tickets - Creates a new ticket
- PUT /tickets/12 - Updates ticket #12
- PATCH /tickets/12 - Partially updates ticket #12
- DELETE /tickets/12 - Deletes ticket #12

Summary, following convention is encouraged:

/cars  

	GET - returns a list of cars
	POST - creates a new car
	PUT - bulk updates cars
	DELETE - deletes all cars

/cars/711

	GET - returns a specific car
	POST - method not allowed (405)
	PUT - updates a specific ticket	
	DELETE - deletes a specific ticket

## Use verb only for special operations ##

For some special operations, such as cancel/refund/return an order, approve/reject an request. We could use verb, but only at the very end.

- PUT /orders/CAEN3824/cancel - Cancel order CAEN3824
- PUT /cancellations/5382912/approve - Approve cancellation request 5382912

## Use concrete names ##

Concrete names are more meaningful for developers over abstract ones. e.g.

- videos
- articles
- books
- deliveries

## Use sub-resources for relations ##
If a resource is related to another resource use subresources.

- GET /cars/711/drivers/ - Returns a list of drivers for car 711
- GET /cars/711/drivers/4 - Returns driver #4 for car 711

## User query parameters to provide filtering, searching, sorting, field selection, paging for collections ##
**Filtering:**

Use a unique query parameter for each field that implements filtering.

- GET /cars?color=red - Returns a list of red cars
- GET /groups?status=active – Returns a list of active groups

**Searching**
If filtering does not fit our needs (to make partial or approximate matches, for instance), we need the power of full text search. 

- GET /cars?name=*suv* - search cars by name which contains suv

Global search. It is used for make fuzzy matching for all fields. Use q=xxx

- GET /cars?q=2015 - fuzzy match all fields which contain 2015

**Sorting:**

Allow ascending and descending sorting over multiple fields.

- GET /cars?sort=-manufactorer,+model - Returns a list of cars sorted by descending manufacturers and ascending models.

**Field selection**

Mobile clients display just a few attributes in a list. They don’t need all attributes of a resource. Give the API consumer the ability to choose returned fields. This will also reduce the network traffic and speed up the usage of the API.

- GET /cars?fields=manufacturer,model,id,color

**Paging**

Use limit and offset. It is flexible for the user and common in leading databases. The default should be limit=20 and offset=0

- GET /cars?offset=10&limit=5

## Version your API ##

Release API with version number. Use a simple ordinal number and avoid dot notation such as 2.5. As a convention, API versioning starting with the letter "v".

- /blog/api/v1

## Handle Errors with HTTP status codes ##

Correctly handling errors facilites the development work with an API. Pure returning of a HTTP 500 with a stacktrace is not very helpful.

Use HTTP status codes for different cases:

- **200 – OK – Eyerything is working**
- 201 – OK – New resource has been created
- 202 - OK - The request has been accepted for processing, but the processing has not been completed. 
- 204 – OK – The server has fulfilled the request but does not need to return an entity-body. e.g. The resource was successfully deleted
- 304 – Not Modified – The client can use cached data
- **400 – Bad Request – The request was invalid or cannot be served. The exact error should be explained in the error payload. E.g. "The JSON is not valid"**
- 401 – Unauthorized – The request requires an user authentication
- 403 – Forbidden – The server understood the request, but is refusing it or the access is not allowed.
- **404 – Not found – There is no resource behind the URI.**
- 422 – Unprocessable Entity – Should be used if the server cannot process the enitity, e.g. if an image cannot be formatted or mandatory fields are missing in the payload.
- **500 – Internal Server Error – If an error occurs in the global catch blog, returns the stracktrace  as response.**

Error payloads

All exceptions should be mapped in an error payload. Here is an example how a JSON payload should look like.

	{
	  "errors": {
	   {
	    "userMessage": "Sorry, the requested resource does not exist",
	    "internalMessage": "No car found in the database",
	    "code": 34,
	    "more info": "http://dev.mwaysolutions.com/blog/api/v1/errors/12345"
	   }
	  ]
	} 

## Examples ##
To get all customers in the Ebay project:

- GET http://ebayhostname/api/customers

To insert (create) a new customer in the Ebay project:

- POST http://ebayhostname/api/customers

To read a customer with customer id 33245:

- GET http://ebayhostname/api/customers/33245

To delete a customer with customer id 33245:

- DELETE http://ebayhostname/api/customers/33245

To update a customer with customer id 33245:

- PUT http://ebayhostname/api/customers/33245

To get all orders for customer with id 33245 in the Ebay project:

- GET http://ebayhostname/customers/33245/orders

To insert (create) a new order for customer with id 33245 in the Ebay project:

- POST http://ebayhostname/api/customers/33245/orders

To get all line items for order 8769 in Ebay project:

- GET http://ebayhostname/api/orders/8769/lineitems

To get all line items for order 8769 of customer 33245 in Ebay project:

- GET http://ebayhostname/customers/33245/orders/8769/lineitems

To insert (create) a new line item for for order 8769 of customer 33245 in Ebay project:

- POST http://ebayhostname/customers/33245/orders/8769/lineitems

### Questions: ###

What is this?

- GET http://www.example.com/customers/33245/orders/8769/lineitems/1
	
		returns only the first line item in that order 8769 of customer 33245.

- GET http://www.example.com/stores/us/customers
		
		returns all customers in us store

- GET http://www.example.com/stores/us/customers?minCost=100
		
		returns all customers in us store who spend at least $100

- PUT http://www.example.com/orders/1234/cancel
	
		cancel order 1234

- GET http://www.example.com/orders/search/CAEN2802
	
		search orders with keyword: CAEN2802

# References #
[http://blog.mwaysolutions.com/2014/06/05/10-best-practices-for-better-restful-api/](http://blog.mwaysolutions.com/2014/06/05/10-best-practices-for-better-restful-api/)

[http://apigee.com/about/blog/technology/restful-api-design-plural-nouns-and-concrete-names](http://apigee.com/about/blog/technology/restful-api-design-plural-nouns-and-concrete-names)

[http://www.restapitutorial.com/lessons/restfulresourcenaming.html](http://www.restapitutorial.com/lessons/restfulresourcenaming.html)

[https://saipraveenblog.wordpress.com/2014/09/29/rest-api-best-practices/](https://saipraveenblog.wordpress.com/2014/09/29/rest-api-best-practices/)

[http://blog.octo.com/en/design-a-rest-api/#plural](http://blog.octo.com/en/design-a-rest-api/#plural)