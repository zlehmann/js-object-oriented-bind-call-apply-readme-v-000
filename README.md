# Bind, Call, and Apply Readme

In this code along, we will be practicing the use of bind, call and apply. You
can follow along using your browser's JS console, or use the provided `index.js`
file to work in by opening `index.html` or running `httpserver`.

## Objectives

1.  Use `call()` and `apply()` to invoke a function with an explicit value for
    `this`
2.  Explain the difference between `call()` and `apply()` in the way you pass
    arguments to the target function.
3.  Use `bind()` to execute functions asynchronously

## Alternate Ways to Invoke Functions

In our exploration of `this`, we saw how it can change depending on how it is
called. Let's take a look at a quick example. Copy and paste this into your
browser's JS console to test for yourself:

```js
function greet() {
	console.log(`my name is ${this.name}, hi!`);
}
greet();
// my name is , hi!

let person = {
	name: 'Bob',
	greet: greet
};

person.greet();
// my name is Bob, hi!
```

We have a function, `greet`, that logs a string. Interpolated into this string
is `this.name`. As we see above, when the `greet` is invoked as a function,
`this` is the global scope. `this.name` doesn't exist at this scope, so we get
`my name is , hi!`.

However, when greet is invoked as a method of _an object_, `this` changes to
equal the object receiving the method call. In the case of `person.greet`,
`greet` is bound to the `person` object. This object has a `name` property, so
`this.name` will produce 'bob'.

What if, however, we wanted to write a function that was separate from a
specific object. Javascript allows us to set `this` to equal whatever we
want. Let's look at that.

```js
function greet() {
	console.log(`my name is ${this.name}, hi!`);
}

let sally = { name: 'Sally' };

greet.apply(sally);
// my name is Sally, hi!

greet.call(sally);
// my name is Sally, hi!
```

As you see above, we can use `call()` or `apply()` to invoke a function with an
explicit value for `this`. So, instead of invoking the `greet()` function
directly, we're invoking the `call()` method or the `apply()` method of the
`greet` function. Our `greet` function can have a method, because in JavaScript
functions are first class objects.

So both `call` and `apply` give us a way to invoke a function and explicitly set
`this` equal to the first argument of `call` or `apply`. So then what's the
difference between `call` and `apply`?

#### Passing Arguments With `call` and `apply`

The only real difference between `call` and `apply` is the way you pass
arguments to the target function.

Let's modify our `greet` function to be a little friendlier:

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

Okay, what about `apply`? So, this works very similar to `call`, except that
`apply` only takes two arguments: the value of `this`, and then an _array_ of
arguments to pass to the target function. So to use `apply` with our new serve
object, we'll need to pass that customer value inside an array.

```js
greet.apply(sally, ['Terry', 'George']);
// Hi Terry and George, my name is Sally!
```

Very similar, but we need to wrap the arguments to the `greet` function in
brackets to make it an array. You can remember the difference because `apply`
takes an **array** (both begin with the letter a). You can use either `call` or
`apply`. The only difference is stylistic.

### bind()

So far, we have been looking at `call` and `apply`, which both explicitly set
`this` and then immediately execute the function call.

Sometimes, however, we want to set the function's `this` value, but delay
calling the function until later. For that, we use `bind()`.

Using `bind` is similar to `call` in that the first argument will be the value
for `this` in the target function, then any arguments for the target function
come in order after that. However, when we use `bind`, we create a _new
function_ with the same capabilities as our original function. The only difference is
that the copied function has the `this` value set, and we can execute that
copied function whenever.

Try this out with our earlier example:

```js
let sally = { name: 'Sally' };

function greet(customer) {
	console.log(`Hi ${customer}, my name is ${this.name}!`);
}

let newGreet = greet.bind(sally);

newGreet('Bob');
// Hi Bob, my name is Sally!

greet('Bob');
// Hi Bob, my name is !
```

As you see from the above code, by calling `greet.bind(sally)`, we return a new
function that we then assign to the variable `newGreet`. Invoking `newGreet`
shows that the `this` object is bound to `sally`.

Note that the original `greet` function is unchanged, as shown by directly
invoking our original `greet` function. `bind` does not change our original
function. Instead, it copies the function, and sets the copied function's `this`
context to whatever is passed through as an argument to bind.

We _could_ use bind and invoke immediately:

```js
greet.bind(sally)('Bob');
// Hi Bob, my name is Sally!
```

But assigning this to a variable like we did with `newGreet` makes this easily
reusable and transferable. In a complex applications, when we invoke functions
in multiple places or components, it is important they are bound to the scope
_we define_, not necessarily the scope they are in.

An example of this would be when `bind` is used to preserve `this` when invoked
in a callback function:

```js
class User {
	constructor(name, favoriteBand) {
		this.name = name;
		this.favoriteBand = favoriteBand;
	}

	favoriteBandMatches(bands) {
		return bands.filter(function(band) {
			return band == this.favoriteBand;
		})[0];
	}
}

let billy = new User('billy', 'paul simon');
billy.favoriteBandMatches(['paul simon', 'the kooks']);
// Uncaught TypeError: Cannot read property 'favoriteBand' of undefined
```

A new `User` instance is created and assigned to `billy`, with a name and
favorite band assigned as properties in the `constructor`.

`favoriteBandMatches` is a class function that takes in an array, `bands`, and
filters it to match the `favoriteBand` property previously set.

Except, when we call `billy.favoriteBandMatches(['paul simon', 'the kooks'])`,
that is not what happens. The problem in our code above is here:

```js
function(band) {
  return band == this.favoriteBand;
}
```

This is just an anonymous function. When it is invoked, `this` is no longer in
the scope of the `User` class. You can confirm this by placing to logs:

```js
class User {
	constructor(name, favoriteBand) {
		this.name = name;
		this.favoriteBand = favoriteBand;
	}

	favoriteBandMatches(bands) {
		console.log('in User scope: ', this.favoriteBand);
		return bands.filter(function(band) {
			console.log('in the anonymous function scope: ', this.favoriteBand);
			return band == this.favoriteBand;
		})[0];
	}
}

let billy = new User('billy', 'paul simon');
billy.favoriteBandMatches(['paul simon', 'the kooks']);
```

The first `console.log` fires fine, but the second fails. To solve this, we can
use `bind`.

```js
class User {
	constructor(name, favoriteBand) {
		this.name = name;
		this.favoriteBand = favoriteBand;
	}
	favoriteBandMatches(bands) {
		console.log('in User scope: ', this.favoriteBand);
		return bands.filter(
			function(band) {
				console.log('in the anonymous function scope: ', this.favoriteBand);
				return band == this.favoriteBand;
			}.bind(this)
		)[0];
	}
}

let billy = new User('billy', 'paul simon');
billy.favoriteBandMatches(['paul simon', 'the kooks']);
// 'paul simon'
```

Let's see why the above code works. The callback function is declared when the
`favoriteBandMatches` method is invoked. When the method is invoked, `this`
equals the `User` instance receiving the method call. We `bind` the callback
function to that `User` instance.

From inside the `filter` method, when the callback function is invoked - the
context would be global here, except that the `this` is already bound to the
`User` instance.

## Summary

We reviewed how `this` works for simple function calls. Then we saw how `call`
and `apply` allow us to instantly execute functions while specifying the `this`
value of the executed function. Then we learned how to use `bind` to make copies
of functions with a new `this` value bound to the copy of the function.

## Resources

- [MDN: Function.prototype.call()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call)
- [MDN: Function.prototype.apply()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
- [MDN: Function.prototype.bind()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

<p class='util--hide'>View <a href='https://learn.co/lessons/js-object-oriented-bind-call-apply-readme'>Javascript bind call and apply</a> on Learn.co and start learning to code for free.</p>
