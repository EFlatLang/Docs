# Operators

This is a list of operators defined for Eb.

## `a . b`

This is the static member access operator, which treats `b` as a string key with which to access a member of `a`

## `a :: b`

This is the static class member access operator, which treats `b` as a string key with which to access a member of the class of `a`. It is short for `a.#type.b`

## `a [ b ]`

This is the dynamic member access operator, which treats `b` as an expression key with which to access a member of `a`

## `# a`

This is the symbol operator, which treats `a` as a string key with which to access any of a number of predefined symbols.

<!--

  ## Pseudo-operators

  ### `#()`

  This is the symbol constructor. A symbol is never equal to anything but itself.

-->