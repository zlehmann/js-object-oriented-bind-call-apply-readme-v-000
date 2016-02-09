# Object Methods 

## Objectives
+ Explain what a method is 
+ Add an action to a constructor function
+ Explain what `this` is in the context of an object

## Intro

Objects have both data and behavoir. So far, we've only learned how to store data in objects. But have no fear, objects in JavaScript can also contain behavoir.

## Actions on Single Object

In Ruby, we'd just add a method to our class:

```ruby
class User
  attr_accessor :name, :email

  def initialize(name, email)
    @name = name
    @email = email
  end

  def say_name
    puts "Hello, my name is #{self.name}"
  end
end
```

We created the method `say_name`, and now each instance of our `User` class has the ability to print their name.

So how do we do that in JavaScript? Let's create a constructor function for our `User` objects.

```js
function User (name, email){
  this.name = name;
  this.email = email;
}
```
 
How do we give our JavaScript user objects the ability to say hello?

We already know how to create functions.  Now we need to attach a function to an object as a property.

```js
function User (name, email){
  this.name = name;
  this.email = email;
  this.sayHello = function(){
    console.log("Hello, my name is " + this.name);
  }
}
```
We've now added the `sayHello` method to our `User` constructor function. A method in JavaScript is a function that is attached to an object via a property. In this case, `sayHello` is a method. 

It's important to note that we use `this` twice in relation to the `sayHello` method. We use it once: `this.sayHello`, where `this` is referencing the object we'll create (as long as we invoke the function with the `new` keyword).  The `this` keyword is probably the most confusing concept in JS so for now let's just assume it works like Ruby's `self` and refers to the instance of the object we're refering to.

Let's make a few users:

```js

carl = new User("Carl", "sparkles@aol.com");

betsy = new User("Betsy", "betsy@flatironschool.com")

george = new User("George", "george@me.com")
```
We can have the user's greet us too:

```js
carl.sayHello();
// prints "Hello, my name is Carl" to the console
betsy.sayHello();
// prints "Hello, my name is Betsy" to the console
george.sayHello();
// prints "Hello, my name is George" to the console
```

But there's a problem here. When build the method directly into the constructor function like this, we're using a lot of space in memory. Every single time a `User` object is created and stored in memory, the `sayHello` function is created and stored in memory with it. What if you're Facebook and have 1.19 billion active users a month? If you were to instantiate all those users at once, you'd be recreating that function in memory 1.19 billion times! (incidentally this is how Ruby does it)

## Add Method to Prototype

Javascript objects have something called a Prototype.  For now, we won't get into an extremely detailed discussion of what Prototypes are, but we will use them as a place to keep our "instance" methods.  In Javascript, when you call a property, the interpreter will look on the instance of the object for a property, and when it finds none, it will look at the Object's Prototype for that property.  If we've attached a function as the property of that name it will call that function in a similar way that Ruby's method lookup chain works.

```js
function User (name, email){
  this.name = name;
  this.email = email;
}

User.prototype.sayHello = function(){
  console.log("Hello, my name is "+ this.name);
}

kevin = new User("kevin", "kevin@aol.com");

kevin.sayHello();
```

For all intents and purposes we've created a JS class that will behave just like the Ruby class we created at the beginning of the readme.  We know how to set properties on the object through the intializer and how to add instance methods to it.

## Resources

+ [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)