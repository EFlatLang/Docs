# First Class Keywords

In Eb, keywords can be created and overridden. This is done with the `#` (symbol) operator, which can be used to refer to keywords without invoking them. Almost any keyword can be referred to in this way, and a funcion can be set to determine its behaviour.

Keywords can also be defined as members on objects. For instance, the behaviour of the `instanceof` keyword depends on its righthand operand, so the `type` metaclass defines behaviour for `instanceof` on its instances. This simplifies the code for the keyword:

```js
  function #instanceof (l, r) -> r.#instanceof(l)
```

For a slightly different use case, the `return` keyword is defined on functions. Since the code of a function is bound to it, `return` can get resolved by the function and does not need a global definition.

```js
  class function {
    #return (l:none, r:?any) -> {
      this.returnValue = r;

      // ...
    }

    // ...
  }
```

## Keyword Functions

A keyword function takes two arguments: a lefthand operand and a righthand operand. If want your keyword function to be a prefix or postfix operator, you can simply set the type of one of them `none`. If you set both to `none`, the keyword will be an expression all on its own.