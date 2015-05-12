---
title:  "Testing asMathableDate"
date:   2015-05-12 23:30:00
categories: javascript date learning jasmine tests
---

After setting up ```asMathableDate``` I decided to add some stuff to it. Like tests and a [Bower](http://bower.io/) repository.

Setting up a bower repository is easy. Just go to the command line and type

```
$ bower init
```

It'll ask you a bunch of questions with a lot of correct predefined answers and then set up a file called ```bower.json``` with the answers you chose.

After that I added [Jasmine](https://jasmine.github.io/) as a dev dependency to my project with

```
$ bower install --save-dev jasmine-core
```

This will add the package ```jasmine-core``` as an entry on the ```bower.json``` file, under ```devDependencies```. Couldn't be more straight then that.

Then you'll have to setup an HTML file which will run the tests. The file must include 3 JavaScript and one CSS file. They are:

* jasmine-core/lib/jasmine-core/jasmine.css
* jasmine-core/lib/jasmine-core/jasmine.js
* jasmine-core/lib/jasmine-core/jasmine-html.js
* jasmine-core/lib/jasmine-core/boot.js

Also, you want to include at least two other files:

* The script you want to test
* The script with the tests

That's it. If everything is fine, when accessing this HTML page you should see the Jasmine logo and a message "No specs found" in gray. So, let's write specs.

The first one I wrote was to make sure the object I'm messing with is still a ```Date``` object. So it looks like:

```javascript
describe('asMathableDate', function () {
	var date;
	beforeEach(function (){
		date = new Date('2015-05-09T08:55:36.614Z');
		asMathableDate.call(date);
	});
	it('still is a Date object', function () {
		expect(date instanceof Date).toBe(true);
	});
});
```

The ```beforeEach``` function is called on the beginning of every spec. So, for every ```it``` callback, we'll work with a clean ```Date``` object with the ```asMathableDate``` functions added. And then we make sure that the other methods are working:

```javascript
describe('asMathableDate', function () {
	it('can add years', function () {
		date.addYear(2);
		expect(date.getUTCFullYear()).toBe(2017);
	});
});
```

Since I was lacking creativity, everything went inside the same callback with a generic name, meaning this spec goes exactly after "still is a Date object". I just wanted to make sure my maths are still good.

I pushed this tests to [the project on Github](https://github.com/msilvagarcia/asMathableDate) and you can use it however you want. Have fun!
