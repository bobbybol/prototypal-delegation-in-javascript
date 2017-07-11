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
The way we create objects from here is to use **factory functions**. No constructors. Factory functions. Let's demonstrate this with a simple example first, where the factory creates a new object from the pre-existing prototype, and then we just add some properties to that object. Finally we return the object, so every time the factory function is called, we create a brand new animal.
```javascript
function createAnimal(genus, species) {
    // Create a new object with `animalPrototype` as its prototype
    var theObject = Object.create(animalPrototype);
    
    theObject.genus = genus;
    theObject.species = species;
    
    return theObject;
}
```
Now we can create objects like so, which have access to all the methods on the prototype:
```javascript
var polarBear = createAnimal('Ursus', 'maritimus');

polarBear.writeName();  // Ursus maritimus
```
We can improve upon the above example, by combining everything into one return statement. This may be preferred for smaller factories, while in larger factories you might want to keep all objects you're using to finally construct your return object from in separate variables. In any case, the short syntax looks like this:
```javascript
function createAnimalAlso(genus, species) {
    return Object.assign(
        Object.create(animalPrototype), 
        {
            genus: genus,
            species: species
        }
    );
}
```
Very cool! Now let's add a closure to that. A closure variable inside a factory function is **truly hidden** from the outside world. Unless you decide to give the animal a method to spoil the secret, of course:
```javascript
function createAnimalAlso(genus, species) {
    var _secretPassword = 'roarrrr';
    
    return Object.assign(
        Object.create(animalPrototype), 
        {
            genus: genus,
            species: species,
            spoilSecret: function() {
                console.log(this.binomialName() + ' goes ' + _secretPassword + '..');
            }
        }
    );
}

var cheetah = createAnimalAlso('Acinonyx', 'jubatus');

cheetah.writeName();    // Acinonyx jubatus
cheetah.spoilSecret();  // Acinonyx jubatus goes roarrrr..
```
