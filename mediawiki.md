**MediaWiki**

[Evaluate math expression](https://www.mediawiki.org/wiki/Help:Extension:ParserFunctions##expr): `{{#expr: expression }}`

[Conditional rendering](https://www.mediawiki.org/wiki/Help:Extension:ParserFunctions##if): `{{#if: test string | return if truthy | return if falsy }}`. Also available: [`#ifeq`](https://www.mediawiki.org/wiki/Help:Extension:ParserFunctions##ifeq) (if strings are equal), [`#iferror`](https://www.mediawiki.org/wiki/Help:Extension:ParserFunctions##iferror), [`#ifexpr`](https://www.mediawiki.org/wiki/Help:Extension:ParserFunctions##ifexpr), [`#ifexist`](https://www.mediawiki.org/wiki/Help:Extension:ParserFunctions##ifexist) (if page exists), [`#switch`](https://www.mediawiki.org/wiki/Help:Extension:ParserFunctions##switch)

Loop through an array: `{{#arraymap: input | delimiter | variable name | code | output delimiter }}`

Comments: `<!-- Comment -->`

Transclude another page: `{{ Page name | optional parameter }}`

MediaWiki supports a number of [magic words](https://www.mediawiki.org/wiki/Help:Magic_words), including: 
- `{{PAGENAME}}`
- `{{FULLPAGENAME}}` (includes the namespace)
- `{{PAGENAMEE}}` (URL-encoded page name)
- `{{__NOTOC__}}` (hides the Table of Contents)
- `{{!}}` (escaped pipe character)
- various date/time functions

Escape wiki markup: `<nowiki> **this** _formatting_ [[won't be processed]]</nowiki>` ([further formatting help](https://www.mediawiki.org/wiki/Help:Formatting))

# Variables

## #vardefine

Define a variable

```
{{#vardefine: variable-name | variable-value }}
```

## #var

Use a variable

```
{{#var: variable-name }}
```

Use a variable, providing a default

```
{{#var: variable-name | default }}
```

# String manipulation

[**Reference guide**](https://www.mediawiki.org/wiki/Help:Extension:ParserFunctions)

Whitespace, including newlines, tabs, and spaces, is stripped from the beginning and end of all the parameters of these parser functions. [Workarounds available.](https://www.mediawiki.org/wiki/Help:Extension:ParserFunctions#Stripping_whitespace)

For most of these, negative parameter values count from the right end.

Other available functions: `len`, `pos`, `rpos`, `sub`

## #replace

Replace within a string

```
{{#replace: input | search | replace}}
```

## #explode

Split a string into pieces and return one or more of them.

```
{{#explode:string|delimiter|starting index|quantity of pieces}}
```

* <code><nowiki>{{#explode:And if you tolerate this| |2}}</nowiki></code> <translate><!--T:2629--> returns</translate> <code>you</code>
* <code><nowiki>{{#explode:String/Functions/Code|/|-1}}</nowiki></code> <translate><!--T:2630--> returns</translate> <code>Code</code>
* <code><nowiki>{{#explode:Split%By%Percentage%Signs|%|2}}</nowiki></code> <translate><!--T:2631--> returns</translate> <code>Percentage</code>
* <code><nowiki>{{#explode:And if you tolerate this| |2|3}}</nowiki></code> <translate><!--T:2632--> returns</translate> <code>you tolerate this</code>

## #titleparts

Get pieces of a page title

```
{{#titleparts: pagename | number of segments to return | index of first segment to return }}
```
`{{#titleparts: Psalm 1/1/Arguments | 2 | 0 }}` -> Psalm 1/1

# Types of braces

{{Double curly braces}} are a [magic word](https://www.mediawiki.org/wiki/Help:Magic_words), a [parser function](https://www.mediawiki.org/wiki/Help:Magic_words#Parser_functions), or a [template](https://www.mediawiki.org/wiki/Help:Templates).

[[Double square brackets]] are a link to an internal page. To change the link text, write `[[page name|link text]]`.

[Single square brackets] are a link to an external page.

# Tables

[Detailed reference, with examples](https://www.mediawiki.org/wiki/Help:Tables)

`{|` Table start

`|+` Table caption (optional, only before first row)

`|-` Row (the first one is optional)

`!` Header cell. Optional. Consecutive table header cells may be added on same line separated by double marks (`!!`) or start on new lines, each with its own single mark (`!`).

`|` Cell. Consecutive table data cells may be added on same line separated by double marks (`||`) or start on new lines, each with its own single mark (`|`).

`|}` Table end

# Semantic MediaWiki queries

[**Ask**](https://www.semantic-mediawiki.org/wiki/Help:Inline_queries#Parser_function_.23ask) queries the wiki database and returns a table:

```
{{#ask:
 [[Category:City]]
 [[Located in::Germany]] 
 |?Population 
 |?Area#km² = Size in km²
 |limit=10
}}
```

[**Show**](https://www.semantic-mediawiki.org/wiki/Help:Inline_queries#Parser_function_.23show) gets a single property value from a single page:

```
{{#show: Demo:Berlin |?Population |default=0}}
```

[Other available parameters:](https://www.semantic-mediawiki.org/wiki/Help:Inline_queries#Standard_parameters_for_inline_queries) [`format`](https://www.semantic-mediawiki.org/wiki/Help:Result_formats), `limit`, `offset`, `sort`, `order`, `headers`, `mainlabel`, `index`, `link`, `default`, `intro`, `outro`, `searchlabel`, format-specific parameters

# Debugging

Replace `{{#vardefine}}` with `{{#vardefineecho}}`, which prints the value in the place it's defined