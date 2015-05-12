---
title:  "Fiddling with Date"
date:   2015-05-08 00:12:00
categories: javascript date learning
---

Yesterday, a coworker was having some trouble with ```Dates``` in JavaScript. This prompted me to study the ```Date``` object a bit more and come to [this excercise](https://github.com/msilvagarcia/asMathableDate).

My first idea was to extend the ```Date``` object and add some properties to it. Unfortunately, [it doesn't work like that](http://stackoverflow.com/questions/6075231/how-to-extend-the-javascript-date-object). ```Date``` objects are not extensible in a "classical" way.

So I took another path: [mixins](https://javascriptweblog.wordpress.com/2011/05/31/a-fresh-look-at-javascript-mixins/). I find this idea really cool and, while I don't like to hurt readability in favor of performance, I did go for the one with the best performance, as it's still pretty clear to read.

And then I started going from "bigger" to "smaller". Manipulating the year is pretty straight-forward:

```javascript
function addYear (amount) {
	if (amount === 0) { return ; }
	this.setYear(this.getUTCFullYear() + amount);
}
```

Do nothing if there's nothing to be done, and using UTC so there's little risk of doing wrong stuff because of time zones. Moving to month:

```javascript
var LAST_MONTH = 11;

function addMonth (amount) {
	if (amount === 0) { return ; }
	var currentMonth = this.getUTCMonth();
	if (currentMonth + amount > LAST_MONTH) {
		this.addYear(1);
		this.setMonth(0);
		return this.addMonth(amount - LAST_MONTH + currentMonth - 1);
	}
	if (currentMonth + amount < 0) {
		this.addYear(-1);
		this.setMonth(LAST_MONTH);
		return this.addMonth(amount + currentMonth + 1);
	}
	this.setMonth(currentMonth + amount);
}
```

This was tricky. The idea of using a constant came from my wife, and I prefer to use a word rather than an arbitrary number. It's 11 because the month is stored as a 0-based integer.

If the user is adding or subtracting too many months, I want to reflect that in years. So I'm setting the date to January of the next year and calling the method recursively for the amount defined by the user minus the number of months left in this year minus 1 to account for it's being forwarded for January. And then applying the same logic when the user is trying to subtract too many months. Just don't forget to ```return``` calling the recursion, or you'll end up calling ```setMonth``` multiple times.

Now, for the days:

```javascript
var MILISECONDS_IN_DAY = 86400000;

function addDay (amount) {
	if (amount === 0) { return ; }
	this.setTime(this.getTime() + amount * MILISECONDS_IN_DAY);
}
```

Way easier. This approach is not available for years or months because I don't know how many miliseconds are in them (months and years don't have the same number of days). But days have the same amount of miliseconds (for computers, at least), so it's pretty simple: use ```getTime``` to get the number of miliseconds since UNIX epoch (1970-01-01), add the amount of milisenconds and call ```setTime``` with the resulting number.

And then I just reproduced this logic for hours, minutes and seconds.

Finish it by wrapping everything in the mixin starting point:

```javascript
var asMathableDate = (function () {
	return function () {
		this.addYear = addYear;
		this.addMonth = addMonth;
		this.addDay = addDay;
		this.addHour = addHour;
		this.addMinute = addMinute;
		this.addSecond = addSecond;
		this.addMilisecond = addMilisecond;
	}
})();
```

This is a caching technique, where the JavaScript engine will compile the inner function once and use a reference for each ```Date``` object you mix this in.

[The code is published on Github](https://github.com/msilvagarcia/asMathableDate) and you can use it however you want. Have fun!
