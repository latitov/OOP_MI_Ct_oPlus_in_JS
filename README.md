# OOP_MI_Ct_oPlus_in_JS

### OOP, Multiple Inheritance, and Cascade thing, and o+ operator in JavaScript.

By Leonid Titov, 2019

_This article contains a solution of how to achieve MI in existing JS, easily. It also contains a lot of critics of how JS is designed, an analysis,
and unlikely-to-be-ever-implemented proposals how it could have been made better._

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
#### 2. Define an object constructor, and then create objects of the constructed type, with the keyword `new`.

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
and the properties are assigned inside a constructor() method.

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


1. First difference is that we have "class" word, which might make some people feel more comfortable and at home...

2. A class can have "static" functions:
	
	```
		class Car {
			static hello() {
				return "Hello";
			}
		}
		h = Car.hello();
	```
	
	It's questionable feature, which allows to mix object prototype (class)
	with package tools, into a single variable-namespace.

3. A class has `.prototype`.

	Another self-contradictory syntax of JavaScript.
	
	I'll explain. As I showed above, a class is basically
	a syntax wrapper for a function, a function which constructs and returns an object for us to use, an instance.
	Below I will provide an example where we want to call a method of another class, _explicitly_ passing an object to it,
	(calling a method from another class on this particular object), and JS provides standard syntax for that, `.call()`.
	So far so good. But to use it, we need to point a method somehow, and if it is defined anonymously in a
	function-constructor, then there's no way to do so. _That's why_ JS designer invented `.prototype`, to solve this,
	and somehow access all properties of "prototype of an object", which doesn't exist actually yet.
	
	Here are both a semantic ambiguity, _and_ a logic contradiction.
	
	Let's start with ambiguity: suppose that we
	accessed and modified a prototype, of which there are already some instances were created and exist. Should it affect them,
	or not? Maybe it should only affect new object, created _after_ the modification? Hope that ECMA standard clarifies this,
	but it's __slippery__.
	
	Now the logic contradiction. Look, there are fundamentally two ways to represent classes: create and keep them as
	a real objects (instances), which are _just used as_ prototypes, and clone them whenever we need another copy (instance);
	or classes are purely virtual, i.e. don't exist as objects, and whenever you want to instantiate it, a new object is created,
	using a class as a description of _how_ to create it. That's how C++ works, and it consumes less memory, as there's
	no need to keep prototypes as actual objects in memory. However, first way is more powerful. __Now__, classes in JS
	try to represent virtual classes, like C++ way. It naturally makes it impossible to modify a prototype, and a need for
	"static" functions (just like in C++ or Java) arises. __But then JS designers introduce prototypes!__ The prototype
	_is_ a real object in terms of memory consumtion, and so invalidates all the benefits of keeping a class virtual.
	__But then, the class scope itself could have been made a prototype__, i.e. a real actual object. Then there would be no
	need to access it's methods from a class directly via .prototype, and there would be no need for static functions, as they
	all will be equal, the same functions can be either called as static functions of a class, or methods of an instance.
	__The `.prototype` construct in JavaScript is totally redundant concept.`
	

4. Inheritance, not multiple, but single inheritance, is possible with "extends" keyword:

	```
		class Car {
			constructor(brand) {
				this.carname = brand;
			}
			present() {
				return 'I have a ' + this.carname;
			}
		}
		class Model extends Car {
			constructor(brand, mod) {
				super(brand);
				this.model = mod;
			}
			show() {
				return this.present() + ', it is a ' + this.model;
			}
		}
		mycar = new Model("Ford", "Mustang");
		document.getElementById("demo").innerHTML = mycar.show();
	```

These are the only benefits of using "class" over older syntax.

## Part 2. MI, The Multiple Ingeritance

The MI, multiple Inheritance. Dreaded, and feared, carefully avoided and misunderstood. The attitude to multiple inheritance,
currently, is very subjective, and most people think that it's something marginal, unnecessary, unnecessarily complex,
ambiguous. Here, I will try to argue with critics of it.

My main argument will be, that MI _is natural_. It's not part of the equation if you, or some, can't figure it out, or implement
properly. In the basement, the MI is a natural thing. Just like OOP is a natural thing. A lot of people hate OOP, because
a lot of people don't understan it properly, and another lot of people misuse it, and manage to create huge and horrific
programware behemoths. But both the concepts of Object and of MI are natural.

The programs we create, are working with concepts of our own universe, the reality of ours, and it's full of things, of Objects.
The computers are well suited to represent some data, _and_ to do some work (execute the code, the programs) on that data.
Nothing new, it's the very foundation of computing. The data in computer represent some phenomenae in real world, the Objects,
and the code is used to do something with that data, do actions. It doesn't matter if you use OOP-enabled language or not, maybe
you are still using punch-cards or assembler, in any case you work with data that represents real world phenomenae, and thus
with objects, even if you don't use that word.

A remarkable example of this is Gtk library, on which half the Linux stands - it's pure C, no single mention
of OOP is there, and still it's clearly object-oriented. Another example, is the Linux kernel itself. Probably, Linus Torvalds was
right when he said that if the sole reason of not using C++ is to keep idiots away, than it's enough reason for not using it.
But still, all those structures in the Linux kernel are nothing but objects, and all function pointers in them are nothing but virtual
methods. The OOP is extremely powerful thing, in a sense that you can realy easily do things, which require considerable
amount of typing work otherwise. This power has a side-effect though - it's easier to mess things up, because to do so so little
effort is needed. It's like a chainsaw - allows to wreak a havoc with too little effort. But that's not a technological problem,
but social, and psychological, and managerial. Technically, OOP is a powerful thing.

Again, no matter what, we anyway work with data that represent objects of reality. OOP just makes this process by an order
of magnitude easier. Just that. It's a standardized infrastructure, and a syntax on top of it. Nothing more. You anyway use,
in one form, or another.

Now, the MI. Just as objects in programs are natural, the MI is natural as well. Let's consider an example, the bottle of water.
This object is simultaneously a water (it has all properties of water, and you can drink it, and do anything else you can
do with water), _and_ it is a bottle (it has all properties of bottle, and you can break it, or recycle, etc.). What is inheritance, anyway?
It's very simple thing. It's when you define a liuqid, and then a juice, which _inherits_ all data and code from liuqid, and
modifies some, and then you can also descend a milk, an oil, a beer... As simple as that. Now you have Packaging, from which
you can descend boxes, wrap paper, containers, and - bottles. And now you have a bottle of water, how are you supposed
to represent such an object? Wouldn't it be _natural_ to just descend it both from a Bottle, and a Water? Yes of course it would.

At this point, people savvy with OOP will complain, _"it's not that simple"_. Come on guys, it's just that, it's _that_ simple.
The fact that some can't figure out how to implement it properly, it's, to make an analogy, isn't the problem with trees,
but with you, if you can manage to get lost in a wood of just three pines. The implementation problem of MI comes from details, particularly
from a questions of what exactly should be done if multiple parents of an object has conflicting parts. For example, what to do, if both
Bottle has a "price", and a water in it also has a "price". Well, in this particular case a composition might be a solution,
i.e. to make an all new object, which _contains_ both a bottle and a water, and price can be obtained as the sum of both.
But, it this case, we lose the main benefit of MI - for example we can't put this objects into an array with other bottles,
and into an array with other liuqids, and then iterate those arrays in uniform ways. Yeah, it's not that simple. But, it's
not a conceptual complexity, but an implementation complexity. Conceptually, MI is still very simple and natural thing.

In fact, the example problem with duplicate price property of bottle and of water is far-fetched, pulled by the ears, as
they say in Russia. Because, inheritance typically happens _before_ objects are used, before they get initialized with
real data. A prototype object, which is to be instantiated, got used to be called a class. So, when "bottle of water" inherits
both from bottle and a water, only one "price" will descend into the "bottle of water". But that is not a problem, because
it's still an abstract bottle of water, a class, and once you instantiate it, once you make a particlar instance of it, it'll get
an actual price, which is a single one for an integral object "this particular bottle of water". In certain OOP infrastructures,
where a class is just another object instance, it might be enough to make a prototype instance of bottle, extend it with a
water, and then use the result as a prototype for subsequent instances of bottles of water.

In the last decades, people carefully avoided MI. Most mainstream languages, designed since 1990, outright
ban the MI, and a lot of scientific articles were published on the harm and unnecessity of MI. It became an outlaw, just
like "goto" (despite the fact, that under the hood, in all computers there is nothing _but_ the jumps (eq. goto)).

A very natural question arises, if MI is so complex, is there alternatives, and maybe it can be avoided, or replaced
with something else? Good question. I know some person, my father actually, who tries to downgrade everything complex
to a question "can we live without it?" Yes we can. The computers, the Internet, can be considered redundant. The cars,
the roads, antibiotics, surgery, dentistry.... All the civilization can be put under a question mark. _Can_, but not necessarily
_should_ be. It's our choice, either be good with simpler things and sit quiet, or invent new things, effectively making our
world more and more complexer. It's a philosophical question, not a technical one. And the same with the ban on MI,
or goto. These are extremely powerful tools. Maybe, if most people would wreak a havoc with them, it's justified that
language designers ban them for good. But it is important to understand that should we be able to harness that power,
we can start using it and upgrade our efficiency as developers the next level up. And it would also help to understand
that to ban something as a solution to a crooked hands problem, is an ungly solution.

In fact, people _do need_ MI, and in languages which lack the MI they start to invent weird workarounds. For example,
interfaces in Java is a clever and useful concept, but using them as a replacement for MI - it's weird. Using composition
instead of MI is the same as suggesting a better air compressor to a flat-tire problem.  Some languages are
MI-enabled from the beginning, like Python and Perl, and nothing wrong happened from that, it's just _natural_ to have
it, once you tackle a problem of a scale of "bottle of water". The C++ also allows MI, but it's implemented in such a way,
that people with experience with it judge it as a bad thing, and that's why all mainstream languages today, except
Perl and Python, ban MI.

### MI, Diamond Problem: The Solution

The problem with MI that gets everyone stuck in the mud, is the question of what descends and what gets masked,
in case parents have conflicting properties.  The solution, in fact, _is very simple_: just make the priority explicit.

Keeping the priority ambiguous, is the foundation of all problems. Once it is made explicit, then the programmer can
decide what has priority over what, and all the questions just disappear.

### Multiple Inheritance in JavaScript

As of now (2019-09-22), MI is absent. Unfortunately. The following _might_ work, but it doesn't:


```
	class Car {
		constructor(brand) {
			this.carname = brand;
		}
		present() {
			return 'I have a ' + this.carname;
		}
	}
	
	class Asset {
		constructor(price) {
			this.price = price;
		}
		est() {
			return 'its estimated price is ' + this.price;
		}
	}
	
	class Model_i1 extends Car, Asset {
		constructor(brand, price, usefulness) {
			// super(brand);
			// super(price);   --- ahhhh, it's ambiguous, this won't work

			// let's write it so, explicitly stating each parent:
			this o+ new Car(brand);
			this o+ new Asset(brand);

			this.usefulness = usefulness;
		}
		show() {
			return `${ this.present() }, ${ this.est()  }, it's ${this.usefulness}% useful.\n`;
		}
	}
	
	mycar = new Model_i1("Ford Mustang", "$100K", 16);
	document.getElementById("demo").innerHTML = mycar.show();
```

This definitely needs explanation. What is 'o+'? And what the hell all this means?

First of all, what do we need? We need exactly this:

1. create an object Car;
2. create an object Asset;
3. extend Car with Asset;
4. the result extend further with "usefulness" property;
	
We can start with Asset instead, and extend it with Car.

So, what does "extend A with B" mean (or equivalently, "B extends/inherits A)"? It means this:
"for all properties of B, write them over to A". That's it. If there was something missing in B, which
exists in A, it'll be left as is; in all conflicts, B's properties will overwrite A's properties. As simple
as that.

Another good word to describe this, is "specialize with", or "specializes". For example, if there is an Animal,
then we can create Elephant which _specializes_ Animal, or say _specialize Animal with Elephant_. I like
"specialize" more than "extend", because the logic that happens there, __"for all properties of B, write
them over to A"__, it's __not__ exactly an extension, because properties get overwritten.

Let's get back to JavaScript. Currently in  JavaScript we use "super" keyword to call parent's constructor,
that's the syntax. And, this syntax _is ambiguous_. Because, by all common sense, we should first
create a base object, and then specialize it (add own properties). But we can write so:


```
	class Model_i1 extends Car {
		constructor(brand, usefulness) {
			this.usefulness = usefulness;
			super(brand);
		}
	}
```

And what will happen then? __I don't know__. Because we first created our own property, and then called parent's
constructor... And if JS somehow would force calling a constructor first, then why the hell it's needed to be typed explicitly
at all, if there's already "extends Car" in the beginning? And, if we call parent's constructor explicitly, then why write
"extends Car"? I don't know. It's a mess. I hope that in ECMA standard it is all explained, but still, it's a mess.
And, it's not even MI, it's merely a single inheritance.

Now, how it _could_ have been impelemented syntactically? Let's look at a list what we need, again:

1. create an object Car;
2. create an object Asset;
3. extend Car with Asset;
4. the result extend further with "usefulness" property;

#### Possible syntax to express this, #1:

```
	class Model_i1 extends Car, Asset {
		constructor(brand, price, usefulness) {
			this.usefulness = usefulness;   // (L1)
		}
		show() {
			return `${ this.present() }, ${ this.est()  }, it's ${this.usefulness}% useful.\n`;
		}
	}
	mycar = new Model_i1("Ford Mustang", "$100K", 16);
```

In this case, Car and Asset constructors are called implicitly, right before the line L1. But, here we have a problem:
what arguments should go to what? Let's re-write it this way then:

```
	class Model_i1(usefulness) extends Car(brand), Asset(price) {
		constructor {
			this.usefulness = usefulness;   // (L1)
		}
		show() {
			return `${ this.present() }, ${ this.est()  }, it's ${this.usefulness}% useful.\n`;
		}
	}
	mycar = new Model_i1(16, "Ford Mustang", "$100K");
```

Now, the compiler can figure out what should go where, and in the aggregated constructor call we specify arguments
in the order as they go in the class declaration, left to right: usefulness, brand, price. Also, the constructor() function
doesn't need to have arguments specified, as that would be redundant.

#### Possible syntax to express this, #2:

```
	class Model_i1 {
		constructor(brand, price, usefulness) {
			this o+ new Car(brand);
			this o+ new Asset(brand);
			this.usefulness = usefulness;
		}
		show() {
			return `${ this.present() }, ${ this.est()  }, it's ${this.usefulness}% useful.\n`;
		}
	}
```
	
It's basically what I wrote in the first place. Here, "o+" operator means "specialize with". This code:

```
	this o+ new Car(brand);
```

Would mean:

```
	c1 = new Car(brand);
	specialize_with(this, c1);
```

And writing "extends ..." in class definition would be redundant, so I intentionally omitted it.

This way is better than first, because you can easily use methods of objects we descended from.
For example, if both Car and Asset has had methods show(), we can write our own show() which
calls those two:

```
	class Model_i1 {
		constructor(brand, price, usefulness) {
			this o+ new Car(brand);
			this o+ new Asset(brand);
			this.usefulness = usefulness;
		}
		show() {
			return Car.prototype.show.call(this) + ", " + Asset.prototype.show.call(this) + ", Model_i1";
		}
	}
```

`.call()` is an existing JS syntax, btw. Unfortunately we can't just call `Car.show.call()`, because Car is a class, i.e. it doesn't
exist, that's why we call it `Car.prototype.show.call`.... See above about this contradictory syntax. But at least in JS all these manipulations _are_ possible!


#### How to implement it in existing JavaScript

Syntax #2 above can readily be implemented in existing JavaScript, except... the syntactic sugar, the "o+"
operator. So, instead of writing:

```
	this o+ new C(args);
```
	
we would write:

```
	specialize_with(this, new C(args));
```

And that's it. Here's how the complete MI code may look like then in existing JS:

```
	class Car {
		constructor(brand) {
			this.carname = brand;
		}
		show() {
			return 'I have a ' + this.carname;
		}
	}
	
	class Asset {
		constructor(price) {
			this.price = price;
		}
		show() {
			return 'its estimated price is ' + this.price;
		}
	}
	
	class Model_i1 {		// extends Car and Asset (just a comment for ourselves)
		//
		constructor(brand, price, usefulness) {
			specialize_with(this, new Car(brand));
			specialize_with(this, new Asset(price));
			this.usefulness = usefulness;
		}
		show() {
			return Car.prototype.show.call(this) + ", " + Asset.prototype.show.call(this) + ", Model_i1";
		}
	}
	
	mycar = new Model_i1("Ford Mustang", "$100K", 16);
	document.getElementById("demo").innerHTML = mycar.show();
```

__This is real code that runs. You can copy-paste it in html file, and try it yourself. It does work.__

__And it implements multiple inheritance in JavaScript.__

And here's the code for specialize_with() utility function:

```
	function specialize_with(o, S) {
		for (var prop in S) {
			o[prop] = S[prop];
		}
	}
```

As short as that. Just 5 LOC. Can even be made one-liner:

```
function specialize_with(o, S) { for (var prop in S) { o[prop] = S[prop]; } }
```

That's the effort to implement MI in JavaScript. Not much of code, more of a know-how.

_Note. This is not the most efficient way to do things, because temporary objects are created and discarded.
But that's why we need language support, to do things both conveniently and in most efficient way._

## Part 3. But wait, there is more use to it!


It's fundamental. As a said previously, an OOP abstraction encompasses almost every aspect of reality,
doesn't matter if you like it or not, doesn't matter if you use this terminology or not.

For example, there are cascade style sheets, CSS.

And there are configuration profiles.

It's the same, let's see an example:

```
	var servlet_profile = {
		"servlet-name": "cofaxCDS",
		"servlet-class": "org.cofax.cds.CDSServlet",
		"init-param": {
			"configGlossary:installationAt": "Philadelphia, PA",
			"configGlossary:adminEmail": "ksm@pobox.com",
			"configGlossary:poweredBy": "Cofax",
			"configGlossary:poweredByIcon": "/images/cofax.gif",
			"configGlossary:staticPath": "/content/static",
			"templateProcessorClass": "org.cofax.WysiwygTemplate",
			"templateLoaderClass": "org.cofax.FilesTemplateLoader",
			"templatePath": "templates",
			"templateOverridePath": "",
			"defaultListTemplate": "listTemplate.htm",
			"defaultFileTemplate": "articleTemplate.htm",
			"useJSP": false,
			"jspListTemplate": "listTemplate.jsp",
			"jspFileTemplate": "articleTemplate.jsp",
			"cachePackageTagsTrack": 200,
			"cachePackageTagsStore": 200,
			"cachePackageTagsRefresh": 60,
			"cacheTemplatesTrack": 100,
			"cacheTemplatesStore": 50,
			"cacheTemplatesRefresh": 15,
			"cachePagesTrack": 200,
			"cachePagesStore": 100,
			"cachePagesRefresh": 10,
			"cachePagesDirtyRead": 10,
			"searchEngineListTemplate": "forSearchEnginesList.htm",
			"searchEngineFileTemplate": "forSearchEngines.htm",
			"searchEngineRobotsDb": "WEB-INF/robots.db",
			"useDataStore": true,
			"dataStoreClass": "org.cofax.SqlDataStore",
			"redirectionClass": "org.cofax.SqlRedirection",
			"dataStoreName": "cofax",
			"dataStoreDriver": "com.microsoft.jdbc.sqlserver.SQLServerDriver",
			"dataStoreUrl": "jdbc:microsoft:sqlserver://LOCALHOST:1433;DatabaseName=goon",
			"dataStoreUser": "sa",
			"dataStorePassword": "dataStoreTestQuery",
			"dataStoreTestQuery": "SET NOCOUNT ON;select test='test';",
			"dataStoreLogFile": "/usr/local/tomcat/logs/datastore.log",
			"dataStoreInitConns": 10,
			"dataStoreMaxConns": 100,
			"dataStoreConnUsageLimit": 100,
			"dataStoreLogLevel": "debug",
			"maxUrlLength": 500
		},
	};
```
	
Now suppose that this is a default profile, and you want to make an instance (copy) of it,
where just few parameters differ. Let's define a specializer structure:

```
	var servlet_profile_spec1 = {
		"servlet-name": "cofaxCDS_spec1",
		"init-param": {
			"useJSP": true,
			"cachePagesRefresh": 111,
			"cachePagesDirtyRead": 111,
		},
	};
```
	
Note, that I defined only those that changed. Now, I can get my specialized instance this way:

```
	specialize_with(servlet_profile, servlet_profile_spec1);
```

That's it.

In case of CSS in HTML, the same thing happens. There is a style (S1) attached to element with style="" property,
then there is class style (S2), and global tag style (S3). Roughly, the style of an element would be:

```
	S0 = copy_of( default_style );
	specialize_with(S0, S1);
	specialize_with(S0, S2);
	specialize_with(S0, S3);
```

Now S0 is the actual style.

## P.S.

If you need a deep_copy() of JS structures, look at https://github.com/latitov/JS_DeepCopy.

<hr>

_Written by Leonid Titov, 2019-09-22_

