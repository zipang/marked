# marked

A full-featured markdown parser and compiler, written in javascript.
Built for speed.

## Plugins

This fork adds a plugin mechanism that is just a basic implementation of the proposition made here :
https://github.com/chjj/marked/pull/35

Each plugin is identified by the special syntax : <code>[<i>pluginName</i>:<i>content</i>]</code>

The plugin implementation must be passed as a *marked* option this way :

``` js
marked.setOptions({
    // See below for the list of available options
    plugins : {
        price: function(arg) {
            return "<span class='price'>" + arg + "</span>";
        }
    }
});
```

so that the markdown example

    * Vintage Darth Vader figure: [price:$75.00]
    * Star Wars Lot - 10 unopened Jar Jar Binks (*sales*): [price:$0.01]

Would be converted with our additional <code>'price'</code> plugin to :

    <ul>
        <li>Vintage Darth Vader figure: <span class="price">$75.00</span></li>
        <li>Star Wars Lot - 10 unopened Jar Jar Binks (<i>sales</i>): <span class="price">$0.01</span></li>
    </ul>

*(a plugin is just a function that will return the transformed version of the intended content.)*

This implementation permits the plugin to live anywhere in the markdown content, and not just as a block element (like was the case with the proposed pull request)

Anyway, this implementation is just a quick and dirty workaround for a badly needed feature, but it cannot be proposed *in its current state* for different reasons :

* The proposed syntax doesn't belong to any Markdown variant and as such, doesn't have any legibility.

    Well, in fact, there isn't *any* proposed syntax for a generic extensions mechanism, because each extension *should* have the most intuitive and natural syntax for *its* purpose, so one syntax doesn't fit all use cases (even if it is not by far the worst proposal that could have been made).

* A more complete plugin mechanism would allow each plugin to specify its own regular expression, and the way to extract parameters from the regular expression.


## Benchmarks

node v0.4.x

``` bash
$ node test --bench
marked completed in 12071ms.
showdown (reuse converter) completed in 27387ms.
showdown (new converter) completed in 75617ms.
markdown-js completed in 70069ms.
```

node v0.6.x

``` bash
$ node test --bench
marked completed in 6448ms.
marked (gfm) completed in 7357ms.
marked (pedantic) completed in 6092ms.
discount completed in 7314ms.
showdown (reuse converter) completed in 16018ms.
showdown (new converter) completed in 18234ms.
markdown-js completed in 24270ms.
```

__Marked is now faster than Discount, which is written in C.__

For those feeling skeptical: These benchmarks run the entire markdown test suite
1000 times. The test suite tests every feature. It doesn't cater to specific
aspects.

## Install

``` bash
$ npm install marked
```

## Another Javascript Markdown Parser

The point of marked was to create a markdown compiler where it was possible to
frequently parse huge chunks of markdown without having to worry about
caching the compiled output somehow...or blocking for an unnecesarily long time.

marked is very concise and still implements all markdown features. It is also
now fully compatible with the client-side.

marked more or less passes the official markdown test suite in its
entirety. This is important because a surprising number of markdown compilers
cannot pass more than a few tests. It was very difficult to get marked as
compliant as it is. It could have cut corners in several areas for the sake
of performance, but did not in order to be exactly what you expect in terms
of a markdown rendering. In fact, this is why marked could be considered at a
disadvantage in the benchmarks above.

Along with implementing every markdown feature, marked also implements
[GFM features](http://github.github.com/github-flavored-markdown/).

## Options

marked has 4 different switches which change behavior.

- __pedantic__: Conform to obscure parts of `markdown.pl` as much as possible.
  Don't fix any of the original markdown bugs or poor behavior.
- __gfm__: Enable github flavored markdown (enabled by default).
- __sanitize__: Sanitize the output. Ignore any HTML that has been input.
- __highlight__: A callback to highlight code blocks.

None of the above are mutually exclusive/inclusive.

## Usage

``` js
// Set default options
marked.setOptions({
  gfm: true,
  pedantic: false,
  sanitize: true,
  // callback for code highlighter
  highlight: function(code, lang) {
    if (lang === 'js') {
      return javascriptHighlighter(code);
    }
    return code;
  }
});
console.log(marked('i am using __markdown__.'));
```

You also have direct access to the lexer and parser if you so desire.

``` js
var tokens = marked.lexer(text);
console.log(marked.parser(tokens));
```

``` bash
$ node
> require('marked').lexer('> i am using marked.')
[ { type: 'blockquote_start' },
  { type: 'paragraph',
    text: 'i am using marked.' },
  { type: 'blockquote_end' },
  links: {} ]
```

## CLI

``` bash
$ marked -o hello.html
hello world
^D
$ cat hello.html
<p>hello world</p>
```

## License

Copyright (c) 2011-2012, Christopher Jeffrey. (MIT License)

See LICENSE for more info.
