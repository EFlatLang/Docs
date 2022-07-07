# Shadow Worlds

## Definition

The term "Shadow World" refers to an environment in which the capabilities of its language become a shadow of its full power.

This refers mostly to using a [domain-specific language][DSL] (DSL) inside of source code written in a [general-purpose_language][GPL] (GPL) where the GPL is much better suited to the task as it is inherently more expressive.

However, one can also make the case that it applies to the semantics of a GPL too, where these semantics restrict the use of language constructs to a single purpose when they could be more broadly applied. In effect, the GPL contains DSLs in its definition due to these restrictive semantics.


## Disadvantages

1. **Code is less idiomatic.**

  DSLs embedded inside a GPL necessarily differ somewhat from the main language. This means that when you study a GPL with Shadow Worlds, you're often learning a single language for the cost of two or more. The greater the difference, the more demanding it is to learn both. A Shadow World may avoid the worst of it, but they are all guilty to some extent. 

  This increased burden is why the ability to write idiomatic code is important. You'll find it easiest to solve a problem when you can express your solution in a style the programming language naturally encourages. So the more of a language's syntax and sematics are domain-independent, the less you will run into the issue of having to write code in a way you're unfamiliar with.

2. **The language is less expressive.**

  This applies to both the GPL itself and the Shadow Worlds it contains.

  When coding in a Shadow World, the lack of expressiveness is the direct result of its limited domain. You can't express certain ideas because the Shadow World simply does not have the means to express them. As a result you'll often need to work with a mix of code with inconsistent style or syntax, because the way you express things in the Shadow World does not match how you express them outside of it.

  When coding in the GPL you may run into this problem as well, though. In many cases, Shadow Worlds exist to cover gaps in a language's features. It may be impossible, slow, or cumbersome to express your idea in the GPL alone, having to resort to using the Shadow World to fully descibe your idea.

  Often, a language tries not to reinvent the wheel when it comes to a particular feature. But let's be clear: writing a new language is already "reinventing the wheel". You're looking for ways to streamline and improve what you didn't like about other languages. And if you fail to propagate those new ideas throughout every aspect of your language, you've failed to make the language you set out to make. Patching a feature gap by domain-specific means is often just lazy, and it's no excuse not to support the feature without the need for a Shadow World in the future.

  There are of course time and cost concerns when it comes to implementing features, so it's not like there is no valid reason to include Shadow Worlds in a language at all. It's better to have poor support for a feature than no support for a feature, after all. But do not get complacent after patching a feature gap with a Shadow World.

3. **Code is more complex.**

  Aside from the more obvious issues


## Context

I use the term "Shadow World" in the broader sense, but it's important to recognise that the exact thing it refers to can differ a bit based on context. So I've included an example of different kinds of Shadow Worlds below:


## Examples


### Manipulation of Web Documents in JavaScript and PHP

[PHP] and [JavaScript] are languages that both handle web documents. Both are dynamically and weakly typed, and both are [C-style][C], multi-paradigm [scripting languages][Scripting]. The main difference is that PHP usually runs on a webserver, while JavaScript runs in the browser. Their purpose and philosophy seem practically identical at a glance, but one of these languages gracefully avoids a potential Shadow World while the other runs headfirst into it without a second thought.

Interestingly enough, JavaScript and PHP both have a questionable reputation with many programmers, so perhaps think for a moment to see if you can figure out which is which in this example!


#### The PHP Way

Let's start with how *not* to avoid Shadow Worlds, featuring PHP.


##### The Trouble With Tags

PHP has no API to manipulate web documents. Instead, you just take the document, give it a `.php` extension, and sprinkle in pieces of PHP that kind of look like [HTML] tags at a glance but which actually don't care about the grammar of HTML at all. In fact, they hardly seem to care about the grammar of PHP either, allowing you to insert them anywhere that's not in the middle of a statement. For instance, the following PHP code is perfectly permissible, and results in the output below.

```php
  <div><?php 
  for ($i = 0; $i < 5; ++$i) { 
  ?>Hi!</div><?php
  }
```

```html
  <div>Hi!</div>Hi!</div>Hi!</div>Hi!</div>Hi!</div>
```

PHP doesn't care that you're inserting too many closing tags by accidentally including the `</div>` part inside the loop. What's worse, since `<?php ?>` is normally thought of like a tag or code block, the hierarchy of your code is completely shattered. Trying to indent this kind of code in a way that's both logical and readable is a bewildering and impossible task.

Because of this feature, you cannot reason about the order or nesting of your HTML tags just based on the HTML, not even when it's apparently safely contained between `?>` and `<?php`. That HTML may be printed once, a hundred times, or never at all. It may appear before or after any other bit of HTML, since it could sit in a subroutine. That subroutine could be called anywhere, including a completely different file!

To make it yet more confusing, you can't even reason about PHP code between `<?php` and `?>` either, as it doesn't have to constitute a syntactically valid program. You can't execute half a block of code, but PHP makes you *think* it can if you just read the code based off the `<?php ?>` tags! Only code between `{` and `}` can be reasoned about this way.

The resulting patchwork of interwoven PHP and HTML is the most straight-forward way to generate a web document with PHP, and it's confusing and ugly as sin. It's somewhat reminiscent of macro metaprogramming, but it feels a lot less robust.


##### Another Perspective

The semantics of `<?php ?>` are confusing and not getting any easier to grasp, so let's try to make sense of it in a different way:

  1. Reverse the tags so it reads like `?> <?php`.
  2. Congratulations, you have invented a crappy version of the string literal. Now, here's why it's worse:

  | `""` | `?><?php`  | Downsides of `?><?php` |
  | ------------- | ------------- | ------------- |
  | | Programs start with an implied `?>` | Its use is mandatory. |
  | | Looks like an HTML tag | It's not used the way its appearance implies. |
  | Is an expression | Is a statement | Its value is unobtainable via code. |
  | Prints nothing | Prints its contents | It's unusable when not trying to print. |

Not only does it make for a crappy string literal, it's not even sufficient for its main purpose, which is manipulation of web documents. Since it lacks any way to manipulate the value inside, when you need to generate content dynamically you just can't use it. As a result, you'll get things like this:

```php
  $items = [
    'foo' => 'bar',
    'qux' => 'quux'
  ];

  <!-- The bad way -->
  <ul><?php
    foreach ($items as $key => $value) {
      ?><li class="<?php
        echo $key;
      ?>"><?php
        echo $value;
      ?></li><?php
    }
  ?></ul>

  <!-- The slightly better way -->
  <ul><?php
    foreach ($items as $key => $value) {
      ?><li class="<?=$key?>"><?=$value?></li><?php
    }
  ?></ul>

  <!-- The string literal way -->
  <ul><?php
    foreach ($items as $key => $value) {
      echo "<li class=\"$key\">$value</li>";
    }
  ?></ul>
```

Trying new things isn't always a bad thing. However, if you need to type *more* and the result is *harder* to read, that's normally a sign to try something different. Yet PHP persists. The language naturally encourages use of `<?php ?>`, even though the result is something that feels like neither pure PHP nor HTML.

PHP supports normal string literals just fine so you mostly won't have to deal with PHP's shitty string literal Shadow World, but just imagine how much worse life would be if you *didn't* have normal string literals. At least PHP dodged that bullet.


#### The JavaScript Way

Okay, so, what's the alternative? The PHP approach is clearly a pretty bad way to do things, but if you want to build up a webpage dynamically, what's the alternative to mixing HTML into your code?

I introduce to you: the [DOM Web API][DOM]!

DOM stands for Document Object Model. In principle, the DOM is little more than a tree data structure. It consists of a bunch of hierarchically ordered nodes with some data, so there's nothing revolutionary here. The more important thing is that the DOM specification matches the kind of tree you get when you parse HTML. The browser needs to do that anyway to present the webpage to you, so it hardly even costs any extra effort to make it available to JavaScript as an API.

The great thing about the DOM is that you can create a document from scratch, or change an existing one completely, all without typing a single bit of HTML code. After all, it's just a first-class object, so you can leverage the full power of JavaScript when manipulating it. The problem with PHP is simply that it lacks a data structure to internally represent a document with, so all you can do is enter data in HTML format manually unless you want to write your own API.

Here is the same thing we tried to do in PHP using the DOM:

```js
  const items = new Map(Object.entries({
    foo: 'bar',
    qux: 'quux'
  }));

  const list = document.createElement('ul');
  items.forEach((text, name) => {
    const item = document.createElement('li');
    item.classList.add(name);
    item.append(text);
    list.append(item);
  });
```
We're creating a list and adding a bunch of items to it in the DOM. Text and a class name are added to each item. It's not quite as short as the string literal way, but even without knowing HTML it's very clear what's going on. The only thing we need to know that the API doesn't handle for us is the meaning of `ul` and `li`. But that's semantics, not syntax, and trivially documented using variable names.

In PHP on the other hand, we need to be know that `<` and `>` deliniate a tag, and that we mustn't forget to close our tags except in a handful of cases, and that a tag can have multiple classes if they are separated by spaces, and thus that a class *can't* have a space... a million little things, each of which is an opportunity for errors or bugs to slip into the code.

JavaScript doesn't even know what a closing tag *is*. It doesn't *need* to know to create a DOM element. All those opportunities for mistakes are suddenly gone. PHP won't even notice if your HTML is malformed because of a typo. JavaScript will, because everything in the DOM is a first-class object, and so it can understand when something has gone wrong. This makes the JavaScript code a lot more robust.


### Regex in JavaScript

An example of a Shadow World in the narrow sense can be seen in [JavaScript]. [Regular expressions][Regex] (Regex) can be used in the language as literals, effectively making JavaScript a superset of Regex. 

```js
  const greeting = /^hello ([\w]+)$/i
```

The Regex Shadow World is not as big a problem as it could be&mdash;its presence does not rob JavaScript of its own ability to match strings. However, since JavaScript cannot be executed inside of a regular expression, the pattern matching that Regex provides JavaScript with lacks the expressiveness that JavaScript itself has.

Now, JavaScript would naturally not be improved by removing Regex, as Regex does offer a lot of features that make pattern matching easier. So before removing Regex can be considered, JavaScript itself would need to inherit those features. In other words, to resolve the Shadow World that is Regex in JavaScript, one could introduce pattern matching as a general language feature that does not run in a domain-specific context.

As an example, below are two snippets of JavaScript, each implementing a basic [BBCode] parser. The first example uses Regex, and then has to inconveniently deal with its lack of ability to match recursively. The second example presumes the existence of a Parser class in which context-free grammars can be easily and (mostly) idiomatically defined.

```js
  // Tag struct
  function Tag (name, arg, content) {
    return { name, arg, content }
  }

  // Parsing function
  export function parseBBCode (sourceText) {
    const tagRegex = /(?<pre>[^\[]*)\[(?<tag>\w+)(?:=(?<arg>[^\]]*))?\](?<in>.*)\[\/\2\](?<post>[^\[]*)/g
    const matches = []

    let match
    while (match = tagRegex.exec(sourceText)) { 
      matches.push(match)
    }

    return matches.length === 0
      ? [sourceText]
      : matches.flatMap(m => [
        m.groups.pre, 
        Tag(m.groups.tag, m.groups.arg, parseBBCode(m.groups.in)),
        m.groups.post
      ].filter(node => typeof node !== 'string' || node.length > 0))
  }

  export const parse = parseBBCode
```

```js
  // Tag struct
  function Tag (name, arg, content) {
    return { name, arg, content }
  }

  // Parser
  const BBCode = new Parser('content', {
    content: rule => rule
      .match('![*', 'tag', 'content')
      .then(m => [m[0].join(''), m[1], ...m[2]]),
      
    name: rule => rule
      .match('\\w+')
      .then(m => m[0].join('')),

    arg: rule => rule
      .match('=', '!]*')
      .then(m => m[1].join('')),

    tag: rule => rule
      .match('[', 'name', 'arg?', ']', 'content?', '[/', 'name', ']')
      .if(m => m[1] === m[6])
      .then(m => Tag(m[1], m[2], m[4])),
  })

  export const parse = BBCode.parse
```

The latter example can easily parse context-free grammars. There are still some Regex-like constructs left that aren't inherently meaningful to JavaScript, but it's a lot more readable and versatile. The syntax we needed to know before consisted of densely packed complex constructs like `(?<name>...)`, `(?:...)` and `[^...]` littered with smaller quantifiers. For this BBCode parser, we just need to know `\\w`, `+`, `?`, `!` and `*`, and different elements are easily visually separated.

For completeness, here is the desired output for a given input:

```js
  import { parse } from './BBCode.js'
  parse('qux [url=https://google.com/]foo [b]bar[/b] baz[/url] quux\nfoo [i]bar[/i] baz')
```
```json
  [
    "qux ",
    { "name": "url", "arg": "https://google.com/", "content": [
      "foo ",
      { "name": "b", "content": [ 
        "bar"
      ] },
      " baz"
    ] },
    " quux\nfoo ",
    { "name": "i", "content": [
      "foo"
    ] }
  ] 
```

### `printf` in C

The [printf format string][printf] is a language feature that has very little descriptive power in its own right. Its format was certainly inspired by older template languages, and other languages have borrowed from it since, but it doesn't exactly feel right to look at it in isolation.

As a domain-specific language, the printf template language runs into the Shadow World problem, even though it's *part* of C. This is to demonstrate that the problem is not necessarily one of outside influence, where you just take something that already exists to save work. It's just as easy to get carried away and not consider the destructive effect a feature can have on the internal consistency or design philosophy of your own language.

The problem printf exists to solve is that concatenating bits and pieces of strings is hard to read and looks ugly. Ideally, you just want to define your string as a single cohesive whole and worry about the values you're going to put in it later. C also allows the string to carry a bunch of presentational information. A format specifier (which is the thing that's actually replaced by other values) in a template string takes the form `%[flags][width][.precision][length]specifier`.

  * The **specifier** determines how the provided value is presented.

    There are a bunch of different formats, but the options are by no means exhaustive. For example, `%a` prints a hexadecimal floating point number, but it lacks options to omit the `0x` at the start or whether or not to use scientific notation. Customising this basically requires you to turn the value into a string yourself and then passing it as `%s` instead to print a string.
    
    There is also `%n` which prints nothing but has the side effect of storing the number of characters printed so far to a provided `signed int` pointer, which is bizarre in a number of ways. Wanting to know the amount of characters printed makes some sense in and of itself, but the way you specify this is really strange and not idiomatic in the slightest.

    In general, the idea of `%` followed by some character is not very idiomatic to start with. At a glance you might wonder whether it's similar in function to a template literal, but when the same format specifier can result in different values without side effects that notion really no longer holds.

  * The **length** determines how the length (`long`, `short`, etc.) of the provided value is interpreted.

    This is just unintuitive again. Is there a reason `printf` won't deduce the length automatically? The compiler certainly complains that the type is incorrect if you forget it.

  * The **precision** determines amount of characters printed.

    This one is inconsistent. For integers it specifies a the minimum amount of digits, for floating point numbers it specifies the amount of digits after the decimal point, *except* for `%g` where it determines the maximum number of significant digits instead. Finally, for strings it determines the maximum amount of characters.

    Also, a funny feature is that `.*` makes `printf` expect an extra argument which holds the desired precision. I suppose someone realised the flaw with specifying everything about presentation in a string literal.

  * The **width** inserts spaces at the start to pad out the printed value to a set length. This one is straightfoward, but also includes the funny `*` feature.

  * The **flags** are miscellaneous. 
    * `-` pads with spaces on the end rather than the start, which is fine.
    * `+` forces numbers to display their sign, which also works just fine.
    * ` ` inserts a space when the sign of a printed number is omitted to help align it. This is also fine.
    * `#` adds a prefix to hexadecimal or octal integers, but also forces floating point numbers to display a decimal point even if no more digits will be printed after it. Perhaps this is why hexadecimal floating point numbers always have a prefix. All the more reason to avoid making a single specifier do multiple things
    * `0` left-pads with zeroes instead of spaces. Why not allow any arbitrary string or character? We were doing so well with `*` earlier.

All of these things lead us to the conclusion that the template string is woefully underpowered and people want way more than it can provide. In the meantime, what may have started as an elegant and compact format has turned into an incomprehensible mess while trying to keep up.

### Template Literals in JavaScript

JavaScript is of course very different from C, but the way it handles the same problem `printf` was meant to solve is pretty revealing. Even if certain aspects of C don't allow it to work quite as loosely as JavaScript in this way, I think there is something to be learned here.

In JavaScript, backticks define a template literal. It allows you to embed an expression inside a string, which permits you to run your entire application from within a string if it strikes your fancy.

```js
  function main() { 
    return 'Hello World' 
  }

  console.log(`Program result: ${main()}`); //> "Program result: Hello World"
```

While this is nice, it seems to not be very special so far. All `${ }` does is allow you to say that the string hasn't ended yet and you're just going to insert some stuff. It helps readability, but you can't exactly define this separately from your arguments, can you? Not so fast, we're missing the most critical feature: tagged templates.

While the syntax is a bit odd, a tagged template allows you to pass the *template itself* to a function. This gives you the delightful ability to do the following:

```js
  const template = (strings, ...keys) => (...values) => 
    [strings[0], ...keys.flatMap((key, i) => [values[key], strings[i + 1]])].join('');
  
  const args = ['John Doe', '"Hello"'];
  const used = template`${0} said ${1}!`;
  const usedby = template`${1} was said by ${0}!`;
  const obvious = template`${0} is the same as ${0}.`;

  console.log(used(...args)); //> John Doe said "Hello"!
  console.log(usedby(...args)); //> "Hello" was said by John Doe!
  console.log(obvious(...args)); //> John Doe is the same as John Doe.
```

The ability to reorder and duplicate arguments is great. For instance, languages may use different word order, which would insert certain elements like names the wrong way around in a normal `printf` call, even if everything else was translated correctly. 

Of course, this is just a simple example. We can run code both in our template and on our template, so the possibilities are nearly endless. You can certainly define a formatting function that works very similarly to `printf` but allows more freedom in its use if you want.

<!-- External Links -->
[BBCode]: https://en.wikipedia.org/wiki/BBCode
[C]: https://en.wikipedia.org/wiki/C_(programming_language)
[DOM]: https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction
[DSL]: https://en.wikipedia.org/wiki/Domain-specific_language
[GPL]: https://en.wikipedia.org/wiki/General-purpose_language
[HTML]: https://en.wikipedia.org/wiki/HTML
[JavaScript]: https://en.wikipedia.org/wiki/JavaScript
[PHP]: https://en.wikipedia.org/wiki/PHP
[printf]: https://en.wikipedia.org/wiki/Printf_format_string
[Regex]: https://en.wikipedia.org/wiki/Regular_expression
[Scripting]: https://en.wikipedia.org/wiki/Scripting_language