# Bind, Call, and Apply Readme

In this code along, we will be practicing the use of `bind`, `call` and `apply`.

You can follow along using your browser's JS console, or use `httpserver` to
serve the provided `index.html`. The HTML file will automatically load
`index.js` and edits you make there will be shown in your browser.

## Objectives

1.  Use `call()` and `apply()` to invoke a function with an explicit value for
    `this`
2.  Explain the difference between `call()` and `apply()` in the way you pass
    arguments to the target function
3.  Use `bind()` to create new functions that are associated to specified
    _contexts_
4.  Use `bind()` to execute functions asynchronously

## Alternate Ways to Invoke Functions

In our exploration of `this`, we saw how it can change depending on how it is
called. Let's take a look at a quick example. Copy and paste this into your
browser's JS console to test for yourself:

```js
function greet() {
	console.log(`my name is ${this.name}, hi!`);
}

greet(); // my name is , hi!

let person = {
	name: 'Bob',
	greet: greet
};

person.greet(); // my name is Bob, hi!
```

We have a function, `greet`, that logs a string. Interpolated into this string
is `this.name`. When the `greet` is invoked as a function, `this` is referring
to the [global object][object], [window][window]. We say that the _context_ for
the `greet()` is `window`. In browser-based JavaScript, `window` is the
"_default context_."

However, when `greet` is invoked as a method of _an object_, `this` changes to
refer to the object the method is invoked in. That is, the _context_
automatically changes to be the containing object. Since `person` has a `name`
property set, `this.name` refers to the value 'bob'.

We've seen here that there are conditions where JavaScript will change the
_context_ ("what `this` is set to) automatically. Developers can also force
functions to be executed in other contexts. Javascript allows us to do this
using the `call` and `apply` methods.

```js
function greet() {
	console.log(`my name is ${this.name}, hi!`);
}

let sally = { name: 'Sally' };

greet.call(sally);
// my name is Sally, hi!

greet.apply(sally);
// my name is Sally, hi!
```

As you see above, we can use `call` or `apply` to invoke a function with a
specified _context_. The _context_ in which the function is to be run is passed
in as the first argument to these methods.

> **NOTE:** Our `greet` function is actually an instance of a `Function` class.
> Because of this a function instance _can also have methods_. Functions are
> things that run, but also things like `{}` in JavaScript.

Both `call` and `apply` let us set the value of `this` to whatever we pass as the first argument. The difference
between the two is how arguments are passed to the function.

#### Passing Arguments With `call` and `apply`

Let's modify our `greet` function to be a little chattier:

```js
function greet(customerOne, customerTwo) {
	console.log(`Hi ${customerOne} and ${customerTwo}, my name is ${this.name}!`);
}
```

Now, when we invoke `greet`, not only do we need to explicitly set `this`, but
we also need to pass values for `customerOne` and `customerTwo`.

Using `call`, we pass the object for `this` as the first argument, followed by
any function arguments in order.

```js
let sally = { name: 'Sally' };

function greet(customerOne, customerTwo) {
	console.log(`Hi ${customerOne} and ${customerTwo}, my name is ${this.name}!`);
}

greet.call(sally, 'Terry', 'George');
// Hi Terry and George, my name is Sally!
```

Great! Now we see the name and the message! What happens if we don't pass any
arguments?

```js
greet.call(sally);
// Hi undefined and undefined, my name is Sally!
```

The call to `apply` works similarly to `call`, except that `apply` only takes
two arguments: the value of `this`, and then an `Array` of arguments to pass to
the function, like so:

```js
greet.apply(sally, ['Terry', 'George']);
// Hi Terry and George, my name is Sally!
```

You can remember the difference because `apply` takes an **array** (both begin
with the letter a). You can use either `call` or `apply`. The only difference
is stylistic. Both exist because sometimes arguments need to be collected or
bundled up (`apply`) versus passed directly (`call`).

### Introduce `bind`

So far, we have been looking at `call` and `apply`, which both explicitly set
`this` and then immediately execute the function call.

Sometimes, we want to take a function, associate it to a _context_ and return a
"_context_-bound" version of the _original function_.

Once we have the "_context_-bound" version of the function we can call it with
`(arguments, arguments, ...)` or `call()` or `apply()` without having to
manually set the context. Let's see it in action.

```js
let sally = { name: 'Sally' };

function greet(customer) {
	console.log(`Hi ${customer}, my name is ${this.name}!`);
}

let newGreet = greet.bind(sally); // newGreet is context-bound to sally

newGreet('Bob');
// Hi Bob, my name is Sally!

greet('Bob');
// Hi Bob, my name is !
```

As you see from the above code, by calling `greet.bind(sally)`, we return a new
function that we then assign to the variable `newGreet`. Invoking `newGreet`
shows that the `this` object is bound to `sally`.

Note that the original `greet` function is unchanged. `bind` does not change it.
Instead, `bind` copies the function, and sets the copied function's `this` context
to whatever is passed through as an argument.

We can actually use bind and invoke immediately:

```js
greet.bind(sally)('Bob');
// Hi Bob, my name is Sally!
```

But this is just a noisy way of doing the same work of `call()` or `apply()`.

## bind(), call(), and apply() in JavaScript code

But assigning this to a variable like we did with `newGreet` makes this easily
**reusable** and **transferable**. In complex applications, there are times when
it is better that `this` refers to the context _we assign_. Until the
introduction of [arrow functions][arrows], every new JavaScript function defined
its own `this` value. Using `bind`, we can prevent this behavior.

Let's imagine we want to create an app that matches user interests with keywords
from upcoming events. We could create a `User` class and be able to assign
properties to user instances, like a name and an array of interests. We can also
include a class function, `matchInterests`, that takes in an event and returns
true if there are any matching keywords:

```js
class Event {
	constructor(title, keywords) {
		this.title = title;
		this.keywords = keywords;
	}
}

class User {
	constructor(name, interests) {
		this.name = name;
		this.interests = interests;
	}

	matchInterests(event) {
		return event.keywords.some(function(word) {
			return this.interests.includes(word);
		});
	}
}

let billy = new User('billy', ['music', 'art', 'movies']);
let freeMusic = new Event('Free Music Show', ['music', 'free', 'outside']);

billy.matchInterests(freeMusic);
// Uncaught TypeError: Cannot read property 'interests' of undefined
```

Here, a new `User` instance is created and assigned to `billy`. A name and
interests are assigned as properties in the `constructor`. We've also created a
new `Event`, with a title and keywords, assigned to `freeMusic`.

`matchInterests` is a class method that takes in an event object, checks to see
if _some_ event keywords are _included_ in the user's interests, and returns
true or false accordingly.

Except, when we call `billy.matchInterests(freeMusic);`,
that is not what happens. The problem in our code above is here:

```js
function(word) {
  return this.interests.includes(word);
}
```

Since every new function defines its own `this` value, when the callback
function is invoked, `this` will be `undefined`. We can see this by logging
inside and outside the function:

```js
class Event {
	constructor(title, keywords) {
		this.title = title;
		this.keywords = keywords;
	}
}

class User {
	constructor(name, interests) {
		this.name = name;
		this.interests = interests;
	}

	matchInterests(event) {
		console.log("'this' is defined: ", this);
		return event.keywords.some(function(word) {
			console.log("'this' is now undefined: ", this);
			return this.interests.includes(word);
		});
	}
}

let billy = new User('billy', ['music', 'art', 'movies']);
let freeMusic = new Event('Free Music Show', ['music', 'free', 'outside']);

billy.matchInterests(freeMusic);
// 'this' is defined:  User {name: "billy", interests: Array(3)}
// 'this' is now undefined:  undefined
// Uncaught TypeError: Cannot read property 'interests' of undefined
```

In the first `console.log`, `this` refers to the `billy` user instance. In the
second, `this` is `undefined`. To solve this problem, we can use `bind`:

```js
class Event {
	constructor(title, keywords) {
		this.title = title;
		this.keywords = keywords;
	}
}

class User {
	constructor(name, interests) {
		this.name = name;
		this.interests = interests;
	}

	matchInterests(event) {
		return event.keywords.some(
			function(word) {
				return this.interests.includes(word);
			}.bind(this) // added to the and of the callback function
		);
	}
}

let billy = new User('billy', ['music', 'art', 'movies']);
let freeMusic = new Event('Free Music Show', ['music', 'free', 'outside']);

billy.matchInterests(freeMusic);
```

Let's see why the above code works. When the `matchInterests` method is invoked,
`this` refers to the `User` instance context receiving the method call. We are
in that context when our callback function is defined. Using `bind` here lets us
keep `this` referring to the `User` context.

## A Brief Look at Arrow Functions

In modern JavaScript, arrow functions don't have their own `this`, so `this`
will refer to whatever context the arrow function was invoked in. Using an arrow
function, we could rewrite `matchInterests` as:

```js
matchInterests(event) {
  return event.keywords.some(word => this.interests.includes(word));
}
```

Here, `this` will refer to the `User` instance context.

## Summary

We reviewed how `this` works for simple function calls. Then we saw how `call`
and `apply` allow us to instantly execute functions while specifying the `this`
value of the executed function. Then we learned how to use `bind` to make copies
of functions with a new `this` value bound to the copy of the function.

## Resources

- [MDN: Function.prototype.call()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call)
- [MDN: Function.prototype.apply()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
- [MDN: Function.prototype.bind()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
- [No Separate This](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#No_separate_this)

[object]: https://developer.mozilla.org/en-US/docs/Glossary/Global_object
[window]: https://developer.mozilla.org/en-US/docs/Web/API/Window
[exec]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this
[arrows]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#No_separate_this

<p class='util--hide'>View <a href='https://learn.co/lessons/js-object-oriented-bind-call-apply-readme'>Javascript bind call and apply</a> on Learn.co and start learning to code for free.</p>
