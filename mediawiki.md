**MediaWiki**

# Syntax

Define variable: `{{#vardefine: variable-name | variable-value }}`

Use variable: `{{#var: variable-name }}`

Use variable, providing a default: `{{#var: variable-name | default }}`

String manipulation: `{{#replace: input | search | replace}}`

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

## Types of braces

{{Double curly braces}} are a [magic word](https://www.mediawiki.org/wiki/Help:Magic_words), a [parser function](https://www.mediawiki.org/wiki/Help:Magic_words#Parser_functions), or a [template](https://www.mediawiki.org/wiki/Help:Templates).

[[Double square brackets]] are a link to an internal page. To change the link text, write `[[page name|link text]]`.

[Single square brackets] are a link to an external page.

## Tables

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