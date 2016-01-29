# Object Methods 

## Objectives
+ Explain what a method is 
+ Add an action to a constructor function
+ Explain what `this` is in the context of an object

## Intro

In Ruby, we're used to creating objects that have both attributes **and** can take actions. So far, we've only learned how to give objects attributes in JavaScript. But have no fear, objects in JavaScript can also take actions

## Actions on Single Object

create constructor function, create object with constructor function, give it a method

a method is a function that is attached to an object via a property

call function

object.method();


## This

objects in JS look just like hashes, but we know they're objects because they're self-reflecting. this references the object


## Add Method to Prototype

in constructor function - operates like initialize, this references whatever object will be created

don't attach function to constructor function - don't want to create the function every single time the object is created, that's expensive - creates thousands of that function. -

don't attach method to object when it's being created (what constructor function does). if create 1000 objects, create 1000 functions

instead, want to attach function to prototype

function Student(name){
  this.name = name;
}

Student.prototype.greet = function(){
  console.log("Hi! I'm " + this.name)
}

## Resources