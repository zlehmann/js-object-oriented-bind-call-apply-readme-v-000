# Object Methods 

## Objectives
+ Explain what a method is 
+ Add an action to a constructor function
+ Explain what `this` is in the context of an object

## Intro

In Ruby, we're used to creating objects that have attributes **and** can take actions. So far, we've only learned how to give objects attributes in JavaScript. But have no fear, objects in JavaScript can also take actions.

## Actions on Single Object

In Ruby, we'd just add a method to our class:

```ruby
class User
  attr_accessor :name, :email

  def initialize(name, email)
    @name = name
    @email = email
  end

  def print_name
    puts "Hello, my name is #{self.name}"
  end
end
```

We created the method `print_name`, and now each instance of our `User` class has the ability to print their name.

So how do we do that in JavaScript? Let's create a constructor function for our `User` objects.

```js
function User (name, email){
  this.name = name;
  this.email = email;
}
```
 
So how do we give our JavaScript user objects the ability to say hello?

We need to rewrite our constructor function:

```js
function User (name, email){
  this.name = name;
  this.email = email;
  this.sayHello = function(){
    console.log("Hello, my name is "+ this.name);
  }
}
```
We've now added the `sayHello` method to our `User` constructor function. A method in JavaScript is a function that is attached to an object via a property. In this case, `sayHello` is a method. 

It's important to note that we use `this` twice in relation to the `sayHello` method. We use it once: `this.sayHello`, where `this` is referencing the object we'll create. We also use it inside the body of the method. Here, it's again referencing the object.

So let's make a few users:

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

But there's a problem here. When build the method directly into the constructor function like this, we're using a lot of space in memory. Every single time a `User` object is created and stored in memory, the `sayHello` function is created and stored in memory with it. What if you're Facebook and have 1.19 billion active users a month? You're creating that single method, that method that only says hello 1.19 billion times and storing it in memory. Thats EXPENSIVE.

## Add Method to Prototype

Instead, we want to attach the method to prototype. Remember, in Ruby we call them classes, but in JavaScript we call them prototypes. 

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

Let's back up a second. In Ruby, let's say you have the string "I love Ruby", and you want to uppercase all the letters. You'd call `"I love Ruby".upcase`. Behind the scenes, Ruby is notices we're calling the `upcase` method, checks to see what object type we're calling it on, recognizes the string, and looks to see if the String class has the method `upcase` defined in it. 

Our `User` prototype is going to follow that exact same lookup chain. We create a user `kevin`. We then call the `sayHello` method. JavaScript is going to see what kind of object `kevin` is, recognize it as a `User` object, and then see if the `User` prototype has the `sayHello` method defined. If it does, it will call the method.


## Resources

+ [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)