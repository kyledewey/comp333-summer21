# Object-Oriented Programming In JavaScript #

JavaScript, like Java and C++, is an object-oriented (OO) language.
Like Java and C++, JavaScript has:

- Objects (complete with their own state)
- Constructors (which create objects)
- Methods (which execute on an object)
- Inheritance (allowing objects to get methods from another source)

However, unlike Java and C++, JavaScript notably is *not* a class-based OO language.
Instead, it is a *prototype-based* OO language.
In one sentence, this means that objects inherit directly from _other objects_, as opposed to classes inheriting from classes.
This has profound impact on both JavaScript's design and how it can be used:

- There is no need to have classes, or most of the machinery associated with classes built-in to the language.
  As a result, the language itself is much simpler than a typical class-based OO language, at least in terms of the sheer number of features the programmer needs to be aware of.
- Classes fundamentally fix what kinds of objects can be created at compile time.
  Typically, in class-based languages, you can't dynamically create a whole new class at runtime (or at least it's difficult to do so).
  However, JavaScript makes it easy to make whole new kinds of objects at runtime, for better or for worse.
- Inheriting from an object is as simple as storing a reference to another object.
  This reference can be updated dynamically.
  As such, not only can we dynamically determine where an object inherits from, we can dynamically change the object it inherits from (again, for better or for worse).

The rest of this guide goes over some of these key concepts in JavaScript.
It is not intended to be an exhaustive guide to everything you can do with this, but rather a brief introduction to how objects work in JavaScript.

### Upfront Caveat: the `class` Reserved Word ###

You may have seen the [`class` reservered word in JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes), which seems to contradict everything you just read.
However, `class` is a bit of a misnomer.
`class` merely sets up the usual prototype-based inheritance to behave according to what you might expect from class-based inheritance.
That is, is basically just translates your class-like code into prototype-based code, transparently.
Prototype-based inheritance is strictly more flexible than class-based inheritance, which is why this is possible to do to begin with: given prototype-based inheritance, we can implement class-based inheritance, but not the other way around.

### Code in this Guide ###

All code in this guide is run with [`node`](https://nodejs.org/en/).
Code input into `node` starts with the `>` character.
The output of `node` immediately follows.
For example:

```javascript
> let x = 1 + 2
undefined
> x
3
```

The above code introduces the new variable `x`, and initializes it to hold the result of `1 + 2`.
This was typed directly into `node`.
Node always responds with an answer, even if no answer makes sense.
In case no answer makes sense, `node` responds with `undefined`, as seen above.
That is, declaring and initializing a variable does not produce any output, so we see `undefined` as a sort of placeholder.
After that, the expression `x` is evaluated, with the line `> x`.
Since `x` was previously declared, node gives us the current value of `x`, namely `3`.

## Objects in JavaScript ##

First, let's talk about objects in JavaScript.
JavaScript objects are implemented as a sort of built-in data structure, representing a map / dictionary / hash table.
It's basically just something that associates keys (represented as strings) to values, where the values can be any data you want.
For example, consider the following code:

```javascript
> let obj = { 'foo': 1, 'bar': true }
undefined
> obj.foo
1
> obj.bar
true
```

The notation with the `{ ... }` is used to create an [object literal](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects).
This basically creates a brand new object in-place.
In the above code, we make an object with the _fields_ `foo` and `bar`, which are both strings.
The word "field" is usually used to refer to individual keys, and sometimes the key/value pair, in an object.
Strings in JavaScript can be denoted with either single quotes (`'foo'`) or double quotes (`"foo"`).
The `foo` field is initialized with the integer `1`, and the `bar` field is initialized with the boolean value `true`.
The subsequent `undefined` is because variable declaration by itself doesn't produce any useful value, so `node` gives us back the dummy value `undefined`.
Afterwards, we access the `foo` and `bar` fields of `obj`, which intentionally uses the usual dot (`.`) syntax for accessing a field of an object.

### Accessing Nonexistent Fields and `undefined` ###

If you access a nonexistent field of an object, then you get back the special value `undefined`.
This is shown in the snippet below.

```javascript
> let obj = { 'foo': 1 }
undefined
> obj.foo
1
> obj.bar
undefined
```

`undefined` itself is a null-like value; it doesn't have any real special meaning by itself, and it just sort of exists.
Note that JavaScript also has `null`, so JavaScript has basically _two different_ null-like values: `undefined` and `null`.
In context, `undefined` is usually to indicate that something weird happened in a way that's built into JavaScript itself, as with accessing a nonexistent field of an object above.
`null` is usually intended to indicate something weird directly in application code, as one might normally use `null` in another language.

### Dynamically Adding or Removing Fields ###

We can dynamically add fields to an object in JavaScript.
For example, consider the following code:

```javascript
> let obj = { 'foo': 1 }
undefined
> obj.foo
1
> obj.bar
undefined
> obj.bar = false
false
> obj.bar
false
```

`obj` initially has no `bar` field, so accessing it gives us back the special value `undefined`.
However, we later add a `bar` field, with the line `obj.bar = false`, and subsequently see that we can now access `obj.bar`.
Note that, as simple as this looks, this is something we fundamentally _cannot_ do in Java or C++: we've effectively dynamically added an instance variable to an object.

We can similarly dynamically remove a field from an object using `delete`, as shown below:

```javascript
> let obj = { 'foo': 1 }
undefined
> obj.foo
1
> delete obj.foo
true
> obj.foo
undefined
```

The object above initially has a `foo` field, but we later remove it.
Subsequent accesses to `foo` give us back `undefined`, as we're now accessing a field that no longer exists.
Once again, this is something that fundamentally cannot be done with Java or C++: we are directly removing an instance variable from an object.

## Constructors ##

While we can directly construct objects using object-literal notation, oftentimes we want to create a lot of objects that have similar fields to each other.
In class-based OO, we might make a class for this purpose, serving as a template for all our objects.
In JavaScript, this same problem is instead solved with _constructors_.
For example, the code below defines a `Rectangle` constructor, and then creates two rectanges, each with their own separate `width` and `height`:

```javascript
function Rectangle(inputWidth, inputHeight) {
    this.width = inputWidth;
    this.height = inputHeight;
}

> let r1 = new Rectangle(3, 4)
undefined
> let r2 = new Rectangle(5, 6)
undefined
> r1.width
3
> r2.height
6
```

The `Rectangle` constructor itself is just a function; from JavaScript's standpoint, there is no special distrinction between regular old functions and constructors.
From a programmer's standpoint, constructors usually start with a capital letter, whereas regular functions usually start with a lowercase letter.

In the code above, we call the constructor using `new`, instead of performing a regular function call.
`new` is specifically used for calling a function as a constructor, and it has the following semantics:

- First create a new, empty object
- Bind a reference to this newly-created object to the built-in variable `this`
- Start executing the called constructor, as if it were a normal function
- Once the function finishes, `new` returns a reference to the newly-created object, irrespective of what the function returns (if anything).
  Constructors don't normally explicitly return anything anyway, but even if they did, this return value is ignored.

Because a new object is created first and bound to `this`, the above code ends up dynamically adding the `width` and `height` fields to the newly-created object.
Furthermore, because `Rectangle` is being called as a function, the parameters to the call are bound to the variables `inputWidth` and `inputHeight`.

## Inheritance ##

Objects can inherit directly from other objects.
Each object has a somewhat special field called `__proto__`, which refers to an object's _prototype_.
`__proto__` is used whenever an attempt is made to access a field which doesn't exist.
To show how this works, let's consider the following access: `obj.myField`.
JavaScript executes the following algorithm for this access:

1. See if `obj` contains field `myField`.
   If it does, then return whatever value is associated with `myField`.
2. If `obj` does not contain the `myField` field, look instead at the `__proto__` field of `obj`.
   If `__proto__` itself is a reference to an object, *recursively* do this lookup procedure of `myField`, but instead starting from the object `__proto__` refers to instead of `obj`.
3. If `obj` does not contain the `myField` field, *and* `obj.__proto__` isn't an object, then return `undefined`.

An example follows:

```javascript
> let obj1 = { 'foo': 1 }
undefined
> let obj2 = { 'bar': 2, '__proto__': obj1 }
undefined
> obj2.bar
2
> obj2.foo
1
> obj2.blah
undefined
```

As shown, `obj2` is given a `__proto__` field which refers to the previously-created object `obj1`.
`obj1` itself has a `foo` field.
With the access `obj2.bar`, this immediately returns `2`, because `obj2` directly has a `bar` field.
However, with the access `obj2.foo`, this gets more interesting.
`obj2` does *not* have a `foo` field, but it _does_ have a `__proto__` field which refers to an object.
As such, this lookup procedure for `foo` is repeated for the object `__proto__` refers to, specifically `obj1`.
In other words, this effectively becomes `obj1.foo`.
`obj1` does, in fact, have a `foo` field, so this lookup ultimately returns `1`.
In this manner, `obj2` is said to inherit from `obj1`: while `obj2` itself doesn't have a `foo` field directly, we can still access a `foo` field from it, because its parent `obj1` has a `foo` field.

As for the last access of `obj2.blah`, since `obj2` itself doesn't have a `blah` field, the lookup consults the `__proto__` field instead.
This effectively becomes `obj1.blah` as a result.
However, `obj1` also lacks a `blah` field, and it doesn't have a `__proto__` field either, so this returns `undefined`.

Since this lookup approach is based on looking at the `__proto__` field, and because the `__proto__` field itself is just another field, we can _dynamically_ change what an object inherits from.
This is shown in the example below:

```javascript
> let base1 = { 'foo': 1 }
undefined
> let base2 = { 'bar': 2 }
undefined
> let obj = { 'baz': 3, '__proto__': base1 }
undefined
> obj.foo
1
> obj.bar
undefined
> obj.__proto__ = base2
{ bar: 2 }
> obj.foo
undefined
> obj.bar
2
```

As shown above, `obj` initially inherits from `base1`; i.e., `obj`'s `__proto__` field initially refers to `base1`.
As such, it appears to have a `foo` field with the later access to `obj.foo`, which returns `1`.
`foo` is coming from `base1` here.
Then, we update `obj`'s `__proto__` field to instead refer to `base2`; i.e., we make `obj` inherit from `base2` instead.
Suddenly, `obj.foo` returns undefined, because `obj` itself lacks `foo`, as does `base2`.
However, `obj.bar` suddently returns `2`, because `obj` inherits `bar` from `base2`.

### Caveat: Slight Oversimplification and Non-Idiomatic Behaviors ###

For the purposes of this class, you don't need to dig any deeper here.
However, beyond the class, you should be aware that `obj1` above does, in fact, have a `__proto__` field, even though we never directly accessed it.
This does, in fact, point to another object, which effectively acts as the ultimate parent of all objects in JavaScript.
This sort of base object itself doesn't have a `blah` field, so `undefined` is still ultimately returned.
We won't be using this sort of base object at all in this class, so you don't need to worry about it.

One other thing: the direct access of `__proto__` is not considered good practice.
Because this can radically change behavior so easily, it's usually discouraged.
Additionally, it's arguably not standards compliant, despite being nearly universally supported.
Direct access also usually has negative performance implications, because it can make the JavaScript engine (the thing executing your JavaScript code) encounter difficulty during optimizations.

For our purposes, my goal here is merely to teach how inheritance works in JavaScript, not best practices for setting up inheritance.
The best practices still require you to know all this material, but strictly add further complications.
As such, this is still a suitable foundation for understanding these concepts.

## Methods ##

Methods can be placed directly on objects.
Like constructors, methods the distinction between a method and a function is merely a matter of perspective to JavaScript.
An example showing methods is below, building off of our `Rectangle` example:

```javascript
function Rectangle(inputWidth, inputHeight) {
    this.width = inputWidth;
    this.height = inputHeight;
    this.getArea = function () {
        return this.width * this.height;
    };
}

> let r = new Rectangle(3, 4)
undefined
> r.getArea()
12
```

As before, `Rectangle` is a constructor that adds the `width` and `height` fields to a newly-created object.
In addition, this version also adds a `getArea` field to the newly-created object.
`getArea` itself holds a function, specifically an [anonymous function](https://en.wikipedia.org/wiki/Anonymous_function) (i.e., it has no name).
The function itself uses `this.width` and `this.height` internally; more on that in a bit.

Let's look at the method call performed here, specifically `r.getArea()`.
Internally, this does two things:

1. This does a lookup of `r.getArea`, using the same algorithm as before.
2. This will call the result of `r.getArea`
   If the result of `r.getArea` is not a function, then an exception is thrown.
   Otherwise, `this` will be bound to whatever the object is we are calling it on, specifically `r` in this case.

Since `this` is bound to `r` during the call to `r.getArea` above, we can access `r`'s `width` and `height` fields with `this.width` and `this.height` in the body of the `getArea` method.
The only reason we know that `getArea` is a method at all, as opposed to any old anonymous function, is specifically because of the way we are calling it: `r.getArea()`.
If we instead did:

```javascript
let r2 = new Rectangle(5, 6)
let temp = r2.getArea // note: no parentheses
                      // this isn't a call, just an access
temp()
```

...the above code wouldn't work the same way.
We still end up calling the same function defined before, but `this` would crucially no longer be bound to `r2`.
`this` would instead be bound to a sort of default object sometimes called the [global object](https://javascript.info/global-object).
As such, this code wouldn't do what we wanted.

## Inheritance with Constructors ##

In the prior example, we added a `getArea` method to `Rectangle` objects.
However, this is usually not a great way to add such methods.
To understand why, think for a moment about what exactly gets allocated each time we execute the `Rectangle` constructor, which is duplicated below for convenience:


```javascript
function Rectangle(inputWidth, inputHeight) {
    this.width = inputWidth;
    this.height = inputHeight;
    this.getArea = function () {
        return this.width * this.height;
    };
}
```

If we execute `new Rectangle(1, 2)`, we will allocate:

- Space for a new, empty object
- Space for the `width` field of the new object
- Space for the `width` value of the new object
- Space for the `height` field of the new object
- Space for the `height` value of the new object
- Space for the `getArea` field of the new object
- Space for the `getArea` value of the new object, specifically the function

Since each `Rectangle` is expected to have its own `width` and `height`, it's unavoidable to need to allocate space for these components.
However, `getArea` is another story.
*All* `Rectangle`'s share the same `getArea` method.
However, currently, each one allocates its own separate memory for the `getArea` field, and similarly each allocates its own space for a separate copy of the `getArea` method itself (the anonymous function).
As such, if we start making lots of `Rectangle`s, we'll end up duplicating the same memory over and over again, wasting space.

For this reason, methods are most commonly implemented through prototype-based inheritance instead.
Using the techniques shown so far, we could do this like so:

```javascript
let getAreaMethod = function () {
    return this.width * this.height;
}

let rectanglePrototype = { 'getArea': getAreaMethod };

function Rectangle(inputWidth, inputHeight) {
    this.width = inputWidth;
    this.height = inputHeight;
    this.__proto__ = rectanglePrototype;
}
```

This sets up a prototype for all `Rectangle` objects, and initializes the `__proto__` field of each `Rectangle` to refer to this prototype.
This way, the `getArea` field is only allocated once (specifically in the prototype), and similarly the `getArea` method itself is only allocated once.
Now all `Rectangle` objects effectively share the same field and method, thanks to the prototype.

However, while we're saving memory, the code above admittedly is bulkier than before.
Additionally, we're directly manipulating the `__proto__` field, which isn't considered good practice.
It turns out that JavaScript has a shorthand way of doing the same thing, shown below:

```javascript
function Rectangle(inputWidth, inputHeight) {
    this.width = inputWidth;
    this.height = inputHeight;
}
Rectangle.prototype.getArea = function () {
    return this.width * this.height;
}
```

It might not look like it, but the above code sets up `Rectangle`'s prototype exactly as before.
To understand what this code is doing, we need two short tangents.

### Tangent 1: Named Functions Are Not Special ##

First of all, named functions in JavaScript aren't that much different than anonymous functions.
When we do:

```javascript
function add(x, y) { return x + y; }
```

...this is basically equivalent to saying:

```javascript
let add = function (x, y) { return x + y; }
```

That is, we introduce a new variable with the name of the function, and bind it to a new anonymous function that does whatever the body of the function does.
As such, our prior `Rectangle` definition is equivalent to:

```javascript
let Rectangle = function (inputWidth, inputHeight) {
    this.width = inputWidth;
    this.height = inputHeight;
}
```

### Tangent 2: Functions are Also Objects ###

Second of all, JavaScript functions are *also* objects.
As such, functions can have their own fields, and these fields can have values.
Lookup on these fields works exactly as it does on other objects.

When you create a function in JavaScript, it automatically is given a field named `prototype`.
Note that `prototype` is *not* the same field as `__proto__`.
The `prototype` field is then initialized to an empty object.
All of this happens transparently when functions are created.

### Putting it All Together ###

With this all in mind, let's revisit the original code, duplicated below for convenience:

```javascript
function Rectangle(inputWidth, inputHeight) {
    this.width = inputWidth;
    this.height = inputHeight;
}
Rectangle.prototype.getArea = function () {
    return this.width * this.height;
}
```

The `Rectangle` constructor itself should make complete sense by this point, though the line after it (starting with `Rectangle.prototype`) might look odd.
With the prior explanation in mind, this subsequent line is adding a `getArea` field to the (previously empty) object referred to by `Rectangle.prototype`.
This `getArea` field is initialized with an anonymous function which will ultimately return the area.

Now let's say we create a new `Rectangle` object, as with `new Rectangle(1, 2)`.
This will go through the usual process of running through a constructor.
However, one additional step occurs that was previously elided: the `__proto__` field of the newly-constructed `Rectangle` object is _automatically_ set to refer to the object that `Rectangle.prototype` refers to.
In other words, the above code is equivalent to:

```javascript
function Rectangle(inputWidth, inputHeight) {
    this.__proto__ = Rectangle.prototype; // added line
    this.width = inputWidth;
    this.height = inputHeight;
}
Rectangle.prototype.getArea = function () {
    return this.width * this.height;
}
```

...but we don't need to (and shouldn't) write the `this.__proto__ = Rectangle.prototype` line.
This just happens automatically when we use the constructor.
This helps keep the code clean, and the use of prototypes in this matter is so common that it makes sense to have a special shorthand for this.

## Comparison to Class-Based Inheritance ##

We'll end this discussion with a comparison to class-based inheritance, with special emphasis on Java and C++.
With class-based inheritance, we must define classes which serve as templates from which to make objects.
Classes can inherit from each other, allowing for reuse.
Usually, class-based inheritance requires us to have special syntax for a variety of situations, for instance:

- Instance variables and methods usually are defined with different syntax
- We need special syntax to denote a class
- We need special syntax to denote inheritance
- We might need special syntax to denote overriding (`virtual` is mandatory in C++, and `@Override` is optional but preferred in Java)

However, JavaScript instead ops for a much simpler, and ironically much more flexible model.
Instance variables and methods are declared using the same syntax; these are just fields on an object.
Because we only have objects, no classes, we don't need special syntax for talking about classes.
Inheritance requires us to understand that the `__proto__` and `prototype` fields are a little bit special, but we don't need special syntax to use them; it's just setting fields of an object.
Overriding is merely a matter of setting `__proto__` to the particular object you want; this doesn't need special syntax, and hardly even a special term.
Perhaps most of all, since everything is determined through the fields of objects, this allows us to do much more at runtime: we can dynamically add and remove both instance variables and methods, and we can even change inheritance hierarchies, and even do so only for specific objects if we so chose; almost everything boils down to setting the fields of an object to the right values.

All that said, this still has some issues.
For one, just because there is far less syntax, the programmer nonetheless needs to be _aware_ of these things.
Having special syntax has an advantage of making it immediately obvious that you're doing something special, so less syntax isn't always better.
Additionally, having lots of flexibility is a double-edged sword: if your code can do more things, it means there are more ways in which your code can do things incorrectly.
For example, while we _can_ modify `__proto__` as much as we want, this is generally considered very poor practice; programs that modify `__proto__` are inherently more difficult to understand than programs that leave it alone, and only use the `prototype` mechanism on constructors.
