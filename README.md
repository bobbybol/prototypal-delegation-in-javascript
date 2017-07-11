# Prototypal Delegation in JavaScript
A(n opinionated) summary of prototypal delegation features in JavaScript

## Why do we want to use prototypal delegation?
-

## A simple setup
A core feature of prototypal delegation is **composability**. Let's declare some objects to compose from. We use the \_partial\_ prefix to denote that these objects are strictly used to compose from.
```javascript
var _partial_binomialName = {
    binomialName: function() {
        return this.genus + ' ' + this.species;
    }
};

var _partial_writeName = {
    writeName: function() {
        console.log(this.binomialName());
    }
};
```
While this is a bit of a contrived example, you can see that we could have many different \_partial\_ objects, and compose many different prototypes and objects from these partials. In this case, let's just build a simple new object that will serve as the prototype for any animal object we might want to create later. We use this `animalPrototype` to hold all the shared methods that all the animal objects need. Each and every object can then 'borrow' these methods, or rather 'delegate' certain functionality to the prototype. This is also some good memory management, as the prototype with these methods gets created only once, and potentially millions of animal objects can all delegate to this single prototype, and therefore do not have to possess these methods themselves.
```javascript
var animalPrototype = Object.assign({}, _partial_binomialName, _partial_writeName);
```
