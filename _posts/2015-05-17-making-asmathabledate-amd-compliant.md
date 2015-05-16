---
title:  "Making asMathableDate AMD-compliant"
date:   2015-05-17 01:06:00
categories: javascript date learning jasmine tests tdd
---

The next step in our journey is to make ```asMathableDate``` [AMD](https://github.com/amdjs/amdjs-api/blob/master/AMD.md)-compliant, as in people can use it with [Require.js](http://requirejs.org/) or any similar library. Also, I decided to take a [test-driven development](https://en.wikipedia.org/wiki/Test-driven_development) approach this time, which simplistically means "write tests first".

By definition, AMD modules are loaded asynchronously. So we'll have to setup our tests a little bit differently:

```javascript
describe('asMathableDate as AMD module', function () {
	beforeEach(function(done) {
		return require(['asMathableDate'], function (asMathableDate) {
			module = asMathableDate;
			return done();
		});
	});
});
```

Before each test, we'll require ```asMathableDate``` and cal the ```done``` function inside the callback. This means that the tests will be ran after the module is loaded.

After that, we write the other specs, like:

```javascript
describe('asMathableDate as AMD module', function () {
	it('registers the AMD module "asMathableDate"', function (done) {
		expect(typeof require('asMathableDate')).toBe('function');
		done();
	});
});
```

With this and a couple more specs we can be sure that whatever we're going to write is going to be alright. Oh, and the tests we wrote before should still be successful, so we know we're not breaking backward compatibility.

Then we run the specs, to make sure these are failing. Although obvious most of the times, you want to make sure you're not using something that you don't know and might make the tests go green.

Now, to the actual code. We'll introduce a block that'll check if AMD is present or not, something like this:

```javascript
function (root, factory) {
	if (typeof define === 'function' && define.amd) {
		define(['exports'], function (exports) {
			return factory(root, exports);
		});
	} else {
		root.asMathableDate = factory(root, {});
	}
}
```

This function will either define the module or export a global variable. We'll use this as a [immediately-invoked function expression](http://benalman.com/news/2010/11/immediately-invoked-function-expression/) on the beginning of the code, and wrapping ```asMathableDate``` in the ```factory``` argument of this function. Something like this:

```javascript
(function (root, factory) {
	if (typeof define === 'function' && define.amd) {
		define(['exports'], function (exports) {
			return factory(root, exports);
		});
	} else {
		root.asMathableDate = factory(root, {});
	}
}(this, function (root, asMathableDate) {
	asMathableDate = (/* prepare the mixin here */);
	return asMathableDate;
}));
```

This should do the trick. When we run the tests again, they should go green.

Keep in mind that AMD uses relative paths when defining an unnamed module. That's the reason that, in the actual code, I've written as ```../../asMathableDate```. To use as we did in this article, ```asMathableDate.js``` should be in the same folder as the root folder for this document.

The code is live [on Github](https://github.com/msilvagarcia/asMathableDate) for you to use it however you want. Have fun!
