# Shadow Worlds

## Definition

The term "Shadow World" refers to an environment in which the capabilities of its language become a shadow of its full power.

This refers mostly to using a [domain-specific language][DSL] (DSL) inside of source code written in a [general-purpose_language][GPL] (GPL) where the GPL is much better suited to the task as it is inherently more expressive.

However, one can also make the case that it applies to the semantics of a [GPL] too, where these semantics restrict the use of language constructs to a single purpose when they could be more broadly applied. In effect, the [GPL] contains [DSL]s in its definition due to these restrictive semantics.

## Context

I use the term "Shadow World" in the broader sense, but it's important to recognise that the exact thing it refers to can differ a bit based on context. Firstly, an example of a Shadow World in the narrow sense can be seen in JavaScript:

```js
  const greeting = /^hello ([\w]+)$/i
```

[Regular expressions][Regex] (Regex) can be used in the language as literals, effectively making [JavaScript] a superset of [Regex]. 

The [Regex] Shadow World is not a huge problem because its presence does not rob [JavaScript] of its expressiveness in matching strings. However, there is at least a slight issue here: since [JavaScript] cannot be executed inside of a regular expression, the pattern matching that [Regex] provides [JavaScript] with lacks the expressiveness that JavaScript has.

Of course I'm not advocating for losing all the features that [Regex] offers. These features are important, and [JavaScript] would not be improved by removing them. What *would* improve [JavaScript] is a more thematic way to match patterns. 

As an example, below is a snippet of [JavaScript] implementing a basic [BBCode] parser. This requires recursive pattern matching, which [Regex] can't achieve on its own. Below that is an example of how this could look if [JavaScript] were given the power of [Regex] without sacrificing its expressiveness.

```js
  // Tag struct
  function Tag (name, arg, content) {
    return { name, arg, content }
  }

  // Parsing function
  function parseBBCode (sourceText) {
    const tagRegex = /(?<pre>[^\[]*)\[(?<tag>\w+)(?:=(?<arg>[^\]]*))?\](?<in>.*)\[\/\2\](?<post>[^\[]*)/g
    const matches = []

    let match
    while (match = tagRegex.exec(sourceText)) { 
      matches.push(match)
    }

    return matches.length === 0
      ? [sourceText]
      : matches.map(m => [
        m.groups.pre, 
        Tag(m.groups.tag, m.groups.arg, parseBBCode(m.groups.in)),
        m.groups.post
      ].filter(node => typeof node !== 'string' || node.length > 0))
  }

  console.log(parseBBCode(
    'qux [url=https://google.com/]foo [b]bar[/b] baz[/url] quux\n' +
    'foo [i]bar[/i] baz'
  ))
```

```js
  // Tag struct
  function Tag (name, arg, content) {
    return { name, arg, content }
  }

  // Node builder
  function Node (m, string) {
    return m
      ? return [m.pre, Tag(m.tag, m.arg, m.content), m.post].flat().filter(node => 
        typeof node !== 'string' || node.length > 0) 
      : [string]
  }

  // Parser
  const BBCode = pattern {
    Node(
      `${pre = `[^\[]*`}
      \[${tag = `\w+`}(=${arg = `[^\]]*`})?\]
        ${content = Node(this`.*`)}
      \[/${this.tag}\]
      ${post = Node(this`[^\[]*`)}`)
  }

  console.log(BBCode(
    'qux [url=https://google.com/]foo [b]bar[/b] baz[/url] quux\n' +
    'foo [i]bar[/i] baz'
  ))
```

As you can see, the latter example defines that it must recurse inside of the pattern, and even specifies how its output is handled. That last bit isn't fully necessary, but it cleans up the output a bit, turning it into an array of strings and tags.

Here is the output of both programs:

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
    " quux ",
    { "name": "i", "content": [
      "foo"
    ] }
  ] 
```

After this, an example in the broad sense: (todo)

<!-- External Links -->
[BBCode]: https://en.wikipedia.org/wiki/BBCode
[DSL]: https://en.wikipedia.org/wiki/Domain-specific_language
[GPL]: https://en.wikipedia.org/wiki/General-purpose_language
[JavaScript]: https://en.wikipedia.org/wiki/JavaScript
[Regex]: https://en.wikipedia.org/wiki/Regular_expression