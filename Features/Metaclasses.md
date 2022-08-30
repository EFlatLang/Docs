# Metaclasses

A metaclass is a class whose instances are classes. Eb supports this concept in a two ways:

* Classes are first-class language constructs, allowing functions to produce classes whose properties are based on the function parameters.
* Classes have built-in metaclass support, allowing you to register a metaclass to which that class belongs.

## Registering a Metaclass

The `class` keyword has an optional template argument to define a metaclass with:

```js
  class<SomeMetaClass> MyClass { /* ... */ }
```

The default value of this argument is the `type` class, registering the created class with Eb's type system. If you do not want to provide a metaclass, you can declare a class with an empty template argument. A class *must* have a metaclass, so in this case, the class will be its own metaclass:

```js
  class<> MyClass { /* ... */ }
```

You can also change the metaclass of a class after declaring it. Something to be aware of in this case is that any code the metaclass would execute when you declare your class isn't executed. You can think of this as bypassing the metaclass's constructor, similar to how setting the prototype of an object bypasses the constructor of that class.

```js
  class<> MyClass { /* ... */ }
  MyClass.class = SomeMetaClass;
```

## The `type` Metaclass

The `type` metaclass is interesting in that it is its own metaclass, so it's considered an instance of itself. It also handles the default type system in Eb, meaning that any instance of `type` follows that logic, and that any class that is *not* a `type` instance does not.

The type system in Eb allows you to ensure that a value or variable is of the correct type:

```js
  const n:number = 'Hello'; // TypeError
```

The right operand of `:` can actually be anything, not just a class. What happens is that the class of the right operand is queried, and must resolve whether the provided value is allowed. That looks somewhat like this for most classes:

```js
  #':' (value) -> value instanceof this, value;

  #instanceof (value) -> value.class extends this;

  #extends (superType) -> {
    return this === superType
      ? true
      : this.super extends superType;
  }
```

On the other hand, for their instances, it often looks more like this:

```js
  #':' (value) -> value instanceof this, new this.class(value);

  #instanceof (value) -> this === value:this.class;
```

In other words, a variable can have a value as its "type". Any value put into it is converted to that value when put into it. You can also use this for unions, so it's not just useful for a single value.

```js
  const numBool:0,1 = '0';
  const n:number = numBool;
```

All of this is defined in the `type` metaclass:

```js
  class<> type {
    constructor (members:Map<string,any>) {
      members.forEach((k, v) => this.#static[k] = v);

      this.#constructor = newMembers -> {
        [...members.#static, ...newMembers.#static].forEach((k, v) => this[k] = v);
        this.#constructor = -> [...members, ...newMembers].forEach((k, v) => this[k] = v);
      }
    }

    static #':' (value) -> value instanceof this, value;
    #':' (value) -> value instanceof this, new this.class(value);

    // ...

  }
```

The `type` metaclass defines a constructor that assigns all the static members as values that can be accessed by instances directly, while non-static members are defined in a constructor that creates instances of `type` instances.

One quirk of this self-metaclass system is that the resulting class object was constructed according to its own logic. This means that, when a new `type` is created with a normal constructor, `static #':'` becomes a member on that instance, while `#':'` is inaccessible until an instance of that instance is created. In other words, `type` behaves like a normal class, not a metaclass. This is why the constructor of a metaclass often needs to be a metaconstructor that applies special rules to its initial instantiation.

The `type` metaconstructor therefore sets its static members to be accessible, but its constructor to behave like a normal class constructor afterwards. This means that the static members are accessible to `type` and all its instances, but not to the instances of those instances.


## Metaclass Characteristics

Members defined in a metaclass will be available to all its instances, just like extending a class. However, the context is slightly different, since an instance will not have direct access to the static members of its class, and the non-static members will be specific to the instance.

<!--A metaclass constructor handles the logic of how a class is created. It initialises all the members defined in the class declaration and can make changes to them as a result. When declaring your own root metaclass you don't have to worry about doing this manually, even when declaring your own constructor. A `#type` call allows you to bootstrap this process. If `#type` is the same -->