---
layout: post
sidebar-align: left
title: "Mongoose Basics"
categories: javascript mongodb mongoose
date:  2016-02-13 19:55:22
author: Pere Pages
---

* TOC
{:toc}

[Mongo Tools](http://docs.mongodb.org/ecosystem/tools/administration-interfaces)
[MEAN.JS](meanjs.org)

Mongoose is more an object modeling tool than a mapper!

## Why?

* Validation
* Defaults
* Query builder
* Pseudo-joins
* Life-cycle management

## Mongo Hosting Options

* Microsoft Azure
* mongolab
* Compose

## Robomongo

Shell-centric cross-platform MongoDB management tool

## Mongoose Schema

```javascript
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

var customerSchema = new Schema({
	name: String,
	address: String,
	city: String,
	state: String,
	country: String,
	zipCode: String,
	createdOn: Date,
	isActive: Boolean
});

var simpleSchema = new Schema({fieldName: schemaType});
```

```javascript
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

// child adress schema...
var addressSchema = new Schema({
	type: String,
	street: String,
	city: String,
	state: String,
	country: String,
	postalCode: Number
});

// parent customer schema...
var customerScema = new Schema({
	name: {
		first: String,
		last: String
	},
	address: [addressSchema],
	createdOn: {type: Date, default: Date.now},
	isActive: {type: Boolean, default: true}
});
```

```javascript
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

// pre-define sub-documents...
var subCategory = {
	name: String,
	description: String,
	isActive: Boolean
	};

var subAnswers = {
	answerText: String,
	isCorrect: Boolean,
	displayOrder: Number
};

var subQuestions = {
	questionType: String,
	questionText: String,
	answers: [ subAnswers ]
};

// Define main document schema...
var quizSchema = new Schema({
	name: String,
	description: String,
	categories: [ subCategory],
	questions: [ subQuestions ]
});
}
```

### Allowed Data Types

- String
- Number
- Date (Object)
- Buffer (Object)
- Mixed (Object)
- ObjectId (Object)
- Array (Array -Object-)

## Creating a Model

```javascript
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

var customerSchema = new Schema({
	name: String,
	address: String,
	city: String,
	state: String,
	country: String,
	zipCode: Number,
	createdOn: Date,
	isActive: Boolean
});

// Build a model from the customer schema....
var Customer = mongose.model('Customer', customerSchema); // if the collection is not provided in the method, Customers in mongodb will be used as a name

// Add a custom property to the schema...
customerSchema.add({discountCode: String});

var DiscountedCust = mongoose.model('DiscountCust', customerSchema);
```

### Saving the model

```javascript
// build Quiz model from schema
var Quiz = mongoose.model('Quiz', quizSchema);

// document instance of a model
var quiz1 = new Quiz({...});

// abbreviated...

quiz1.save(callback);
```

## Quering

```javascript
var Standup = require('../models/standup.server.model.js');

// No callback... deferred execution
var query = Standup.find();

// With callback... Executes immediately
Standup.find(function (err,results) {
	// handle the error... or results here
});

// With callback and query conditions
Standup.find({memberName: 'David'}, function (err, results)) {
	// handle the error... or results here
}
```

### Limit returned fields
```javascript
// Limit the returned fields...
Standup.find({memberName: 'Mary'}, 'memberName impediment'),
	function(err,results) {
		//handle the error... or results here
	});
```

### Find one

```javascript
// No callback... No conditins...
var query = Standup.findOne();
query.exec(function(err,results) {
	//handle the error.... or results here
});

// With conditions...
var query = Standup.findOne({memberName: 'Mark'});
```

### Find by ID

```javascript
// By Id... No condtions...
var query = Standup.findById(id);
query.exec(function (err,doc) {
	// handle the error or results here
});

// By id ... return every field but impediment...
var query = Standup.findById(id, '-impediment');
```

## Comparison Operators

- $gt *greater than*
- $gte *greater than or equal to*
- $in *exisits in*
- $lt *less than*
- $lte *less than or equal to*
- $ne  *not equal to*
- $nin *does not exist*

```javascript
Customer.find({discount: {$gte:10}, function (err,results) {
	if (err) throw err;
	console.log(results);
});
```

```javascript
// discount could be also a nested value
Customer.where('discount').gte(10).lt(20).exec(function (err,results) {
	if(err) throw err;
	console.log(results);
});
```

## Updating Documents

Model.update(conditions,update[,options][,callback])

```javascript
var Standup = require('../models/standup.server.model.js');

var condition = {memberName: 'Mary'};
var update =  {impediment: 'None - Mary no longer works here!'};

Standup.update(condtion,update, function (err,numberAffected, rawResponse) {
	// Handle error or raw results here...
});

// finding a document - then updating it ...
Standup.findOne({ memberName: 'Mary'}, function (err,doc) {
	// Handle errors here... Validate document results... etc.
	doc.impediment = 'None - Mary won the lottery...';
	doc.save(function (err) {
		// Handle errors
	});
});
```

### Options

- safe (defaults to true)
- upsert (false)
- multi (false)
- strict -overried the strict option for this update
- overwrite (false)

## Removing Documents

Model.remove(conditions[,callback])

Model.findByIdAndRemove(id[,options][,callback])

```javascript
var condition = {memberName: 'Mary'};

Standup.remove(conditon, function(err) {
	// Handle error here...
});

// Remove any document created on or after Halloween day
var gteDate = new Date(2014, 10, 31);
Standup.remove({createdOn: {$gte: gteDate}}, function (err) {
	// Handle error here...
});
```

#### Options

- new (defaults to true)
- upsert (false)
- select

## Validation 

- Built-in Validators
- Custom Validators
- Handling Errors
- Middleware

### Schema Type

All of them  have **required** but

**String** has

- enum
- match

**Number** has

- min
- max

```javascript
// option 1
// Required Validator Example
var customerSchema = new Schema({
	name: {type: String, required: true},
	address: String,
	city: String,
	state: String,
	country: String,
	zipCode: Number,
	createdOn: Date,
	isActive: Boolean
});

// option 2
// After the schema is defined - via the path API
customerSchema.path('city').required(true, 'Error message here');
```

### Enum

```javascript
// String - Match Validator Example
var reMatch = /[a-zA-Z]/;
var customerSchema = new Schema({
	name: { type: String,
	required: true,
	match: reMatch},

	//...
});
```

### Match

```javascript
// String - Enum Validator Example
var impediments = ['none','minor','blocking','severe'];

var standupSchema = new Schema({
	//...
	impediment: { type: String,
	required: true,
	enum: impediments}
});
```

### Numbers

```javascript
// Customers must receive at least a 5% discount
var customerSchema = new Schema({
	name: String,
	//...
	discount: {type: Number, min: 5}
});

// Customers not allowed more than 60% discount
var customerSchema = new Schema({
	name: String,
	//...
	discount: {type: Number, max: 60}
});

// Customers allowed a discount between 5% and 60% only
var customerSchema = new Schema({
	name: String,
	//...
	discount: {type: Number, min:5, max: 60}
});
```

## Middleware

Middleware are functions which give us execution control

- init
- validate
- save
- remove

### Save

```
Save -> Default Applied -> Validation -> Error
```

```javascript
// Middleware execution flow example...
var personSchema = new Schema({
	firstName: {type: String, required: true},
	lastName: {type: String, required: true},
	status: {type: String, required: true, default: 'Alive'}
});

// Build a model form the person schema
var Person = new mongoose.model('Person', personSchema);

// new document instance of a Person model
var newPerson = new Person({firstName: 'John', lastName: 'Doe'});

// Save the document... Internal validation (required) kicks off now
newPerson.save(function (err) {
	// saved the person document!
});
```

#### Custom Validators

The return values must be either **true** or **false**.

```javascript
// Custom validation - method signature = validate(obj, [errorMsg])
var sizeValidator = [
	function (val) {
		return (val.length > 0 && val.length <= 50)
	},
	// Custom error text...
	'String must be ...' ];

var personSchema = new Schema({
	firstName: {type: String, required: true, validate: sizeValidator},
	lastName: {type: String, required: true, validate: sizeValidator},
	status: { type: String, required: true, default: 'Alive'}
});

// Build a model form the person schema
var Person = new mongoose.model('Person', personSchema);

// New document instance of a Person model
var newPerson = new Person({firstName: 'John', lastName: 'Doe'});

// Save the document... and validate
newPerson.save(fucntion (err) {
	if (err) return handleError(err);
	// saved the person document!
})
```

## Handling Errors

We can also change the built in validators:

```javascript
var msg = module.exports = exports = {};

msg.general = {};
msg.general.default = "Validator...";
msg.general.required = "Path...";

//...
```

```
{PATH} {VALUE} {TYPE} {MIN} {MAX}
```

```javascript
var entry = new Standup({
	//...
});

// validate now...
entry.schema
.path('memberName')
.validate(function (value) {
	return value != 'None'
}, 'You must select a team name.');
```

More validators

```javascript
var memberNameValidator = [
	function (val) {
		return (val.length > 0 && val.toLocaleLowerCase() != 'none')
	},
	// Custom error text
	'Select a valid member name'
];

var requiredStringValidator = [
	function (val) {
		var testVal = val.trim();
		return (testVal.length > 0)
	},
	// custom error text
	'{PATH} cannot be empty'
];
```

We can just put the validation inside the schema

```javascript
// Notes Schema definition
var standupSchema = new Schema({
    memberName: {
        type: String,
        required: true,
        validate: memberNameValidator
    },
    project: {
        type: String,
        required: true,
        validate: requiredStringValidator
    },
    workYesterday: {
        type: String,
        required: true
        validate: requiredStringValidator
    },
    workToday: {
        type: String,
        required: true,
        validate: requiredStringValidator
    },
    impediment: {
        type: String,
        required: true,
        validate: requiredStringValidator
    },
    createdOn: {
        type: Date,
        default: Date.now
    }
});
```

**Saving the model**

```javascript
// controller action
exports.create = function(req, res) {
    var entry = new Standup({
        memberName: req.body.memberName,
        project: req.body.project,
        workYesterday: req.body.workYesrday,
        workToday: req.body.workToday,
        impediment: req.body.impediment
    });

    entry.save(function (err) { //the callback is used to handle errors
    	if(err) {
    		var errMsg = 'Sorry, there was an error saving the stand-up meeting note' + err;
    		res.render('newnote', {title: 'Blah - error', message: errMsg});
    	} else {
    		console.log('Stand-up meeting note was saved!');
    		// redirect to the home page to display list of notes...
    		res.redirect(301, '/');
    	}
    }); 

};
```