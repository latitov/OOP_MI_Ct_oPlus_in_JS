# OOP_MI_Ct_in_JS

### OOP, Multiple Inheritance, and Cascade thing, in JavaScript. By Leonid Titov, 2019

_This article contains a solution of how to achieve MI in JS_.

_Though written in context of the JavaScript, this might also be of particular interest in general, no matter what language you use._

_By "cascade thing" I mean cascading of styles (e.g. CSS), profiles, configuration, whatever else._

_This is the result of me trying to somehow grasp all-in-one and make a cheatsheet of OOP in JavaScript. Courtesy of w3school.com. I do know quite well, to say a least, what the OOP is and how it works in so many other languages, so here it was an attempt not to understand OOP (I already do), but to make a sense of what is what in JS specifically, and somehow get a whole picture of various ways and available means._

_This is in no way an attempt to explain OOP, nor make a complete reference of OOP in JavaScript; rather, if you read this, it is expected that you are deeply familiar with all concepts of OOP, and know few languages where it is implemented differently. If so, this article will be of interest and of use for you, and will be both a fun read, and will make you think._

So let's get started.

## Part 1. OOP Cheatsheet for JavaScript

Normally, objects in JS can be created in these ways:

#### 1. Define and create a single object, using an object literal.

```
	var person = {firstName:"John", lastName:"Doe", age:50, eyeColor:"blue",
		method1: function() { return this.firstName + " " + this.lastName; }
	};
```

Note, this:

```
	var person = {firstName:"John", lastName:"Doe", age:50, eyeColor:"blue"};
```

is identical to this:

```
	var person = new Object();
	person.firstName = "John";
	person.lastName = "Doe";
	person.age = 50;
	person.eyeColor = "blue";
```
	
and also to this:

```
	var person = {};
	person.firstName = "John";
	person.lastName = "Doe";
	person.age = 50;
	person.eyeColor = "blue";
```
#### 2. Define an object constructor, and then create objects of the constructed type, with the keyword new.

```
	// Constructor function for Person objects
	function Person(first, last, age, eye) {
		this.firstName = first;
		this.lastName = last;
		this.age = age;
		this.eyeColor = eye;
	}
	// Create two Person objects
	var myFather = new Person("John", "Doe", 50, "blue");
	var myMother = new Person("Sally", "Rally", 48, "green");
```
	
The above can be thought of as, and is equivalent of, this:

```
	function Person_new(o, first, last, age, eye) {
		o.firstName = first;
		o.lastName = last;
		o.age = age;
		o.eyeColor = eye;
		return o;
	}
	var myFather = Person_new({}, "John", "Doe", 50, "blue");
	var myMother = Person_new({}, "Sally", "Rally", 48, "green");
```
	
The first version is easier to write and generally nicer, at the expense of
some ambiguity.

#### 3. Classes

ES6, also known as ECMAScript2015, introduced classes. A class is a type of function,
but instead of using the keyword function to initiate it, we use the keyword class,
and the properties is assigned inside a constructor() method.

```
	class Car {
		constructor(brand) {
			this.carname = brand;
		}
	}
	mycar = new Car("Ford");
```
	
This can be written in the above #2 way, so:

```
	function Car(brand) {
		this.carname = brand;
	}
	mycar = new Car("Ford");
```
	
or so:

```
	function Car_new(o, brand) {
		o.carname = brand;
	}
	mycar = Car_new({}, "Ford");
```
	
Now, the question is, HOW THIS IS DIFFERENT FROM THE PREVIOUS SYNTAX?



## Part 2. 

## Part 3.

