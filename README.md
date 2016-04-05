# CompareXML

[![Gem Version](https://badge.fury.io/rb/compare-xml.svg)](https://rubygems.org/gems/compare-xml)

CompareXML is a fast, lightweight and feature-rich tool that will solve your XML/HTML comparison or diffing needs. its purpose is to compare two instances of `Nokogiri::XML::Node` or `Nokogiri::XML::NodeSet` for equality or equivalency.

**Features**

 - Fast, light-weight and highly customizable
 - Compares XML/HTML documents and document fragments
 - Can produce both detailed diffing discrepancies or execute silently
 - Has the ability to exclude specific nodes or attributes from all comparisons



## Installation

Add this line to your application's Gemfile:

```ruby
gem 'compare-xml'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install compare-xml



## Usage

Using CompareXML is as simple as

```ruby
CompareXML.equivalent?(doc1, doc2)
```

where `doc1` and `doc2` are instances of `Nokogiri::XML::Node` or `Nokogiri::XML::NodeSet`.

**Example**

Suppose you have two files `1.html` and `2.html` that you would like to compare. You could do it as follows:

```ruby
doc1 = Nokogiri::HTML(open('1.html'))
doc2 = Nokogiri::HTML(open('2.html'))
puts CompareXML.equivalent?(doc1, doc2)
```

The above code will print `true` or `false` depending on the result of the comparison.

> If you are using CompareXML in a script, then you need to require it manually with:

```ruby
require 'compare-xml'
```


## Options

CompareXML has a variety of options that can be invoked as an optional argument, e.g.:

```ruby
CompareXML.equivalent?(doc1, doc2, {squeeze_whitespace: true, verbose: true})
```


----------


- ####`ignore_attr_order: {true|false}` default: **`true`**

    When `true`, all attributes are sorted before comparison and only attributes of the same type are compared.

	**Usage Example:** `CompareXML.equivalent?(doc1, doc2, {ignore_attr_order: true})`

    **Example:** When `true` the following HTML strings are considered equal:

		<a href="/admin" class="button" target="_blank">Link</a>
		<a class="button" target="_blank" href="/admin">Link</a>

	**Example:** When `false` the above HTML strings are compared as follows:

		href="admin" != class="button

	The comparison of the `<a>` element will stop at this point, since a discrepancy is found.

	**Example:** When `true` the following HTML strings are compared as follows:

		<a href="/admin" class="button" target="_blank">Link</a>
		<a class="button" target="_blank" href="/admin" rel="nofollow">Link</a>

		class="button"  == class="button"
		href="/admin"   == href="/admin"
		                =! rel="nofollow"
		target="_blank" == target="_blank"


----------


- ####`ignore_attrs: {css}` default: **`{}`**

    When provided, ignores all **attributes** that satisfy a particular rule using [CSS selectors](http://www.w3schools.com/cssref/css_selectors.asp).

	**Usage Example:** `CompareXML.equivalent?(doc1, doc2, {ignore_attrs: ['a[rel="nofollow"]', 'input[type="hidden"']})`

    **Example:** With `ignore_attrs: ['a[rel="nofollow"]', 'a[target]']` the following HTML strings are considered equal:

		<a href="/admin" class="button" target="_blank">Link</a>
		<a href="/admin" class="button" target="_self" rel="nofollow">Link</a>

	 **Example:** With `ignore_attrs: ['a[href^="http"]', 'a[class*="button"]']` the following HTML strings are considered equal:

		<a href="http://google.ca" class="primary button">Link</a>
		<a href="https://google.com" class="primary button rounded">Link</a>


----------


- ####`ignore_comments: {true|false}` default: **`true`**

    When `true`, ignores comments, such as `<!-- This is a comment -->`.

	**Usage Example:** `CompareXML.equivalent?(doc1, doc2, {ignore_comments: true})`

    **Example:** When `true` the following HTML strings are considered equal:

		<!-- This is a comment -->
		<!-- This is another comment -->

	**Example:** When `true` the following HTML strings are considered equal:

		<a href="/admin"><!-- This is a comment -->Link</a>
		<a href="/admin">Link</a>


----------


- ####`ignore_nodes: {css}` default: **`{}`**

    When provided, ignores all **nodes** that satisfy a particular rule using [CSS selectors](http://www.w3schools.com/cssref/css_selectors.asp).

	**Usage Example:** `CompareXML.equivalent?(doc1, doc2, {ignore_nodes: ['script', 'object']})`

    **Example:** With `ignore_nodes: ['a[rel="nofollow"]', 'a[target]']` the following HTML strings are considered equal:

		<a href="/admin" class="icon" target="_blank">Link 1</a>
		<a href="/index" class="button" target="_self" rel="nofollow">Link 2</a>

	 **Example:** With `ignore_nodes: ['b', 'i']` the following HTML strings are considered equal:

		<a href="/admin"><i class"icon bulb"></i><b>Warning:</b> Link</a>
		<a href="/admin"><i class"icon info"></i><b>Message:</b> Link</a>


----------


- ####`ignore_text_nodes: {true|false}` default: **`false`**

    When `true`, ignores all text content. Text content is anything that is included between an opening and a closing tag, e.g. `<tag>THIS IS TEXT CONTENT</tag>`.

	**Usage Example:** `CompareXML.equivalent?(doc1, doc2, {ignore_text_nodes: true})`

    **Example:** When `true` the following HTML strings are considered equal:

		<a href="/admin">SOME TEXT CONTENT</a>
		<a href="/admin">DIFFERENT TEXT CONTENT</a>

	**Example:** When `true` the following HTML strings are considered equal:

		<i class="icon></i>  <b>Warning:</b>
		<i class="icon>  </i>    <b>Message:</b>


----------


- ####`squeeze_whitespace: {true|false}` default: **`true`**

    When `true`, all text content within the document is trimmed (i.e. space removed from left and right) and whitespace is squeezed (i.e. tabs, new lines, multiple whitespaces are all replaced by a single whitespace).

	**Usage Example:** `CompareXML.equivalent?(doc1, doc2, {squeeze_whitespace: true})`

    **Example:** When `true` the following HTML strings are considered equal:

		<a href="/admin">   SOME TEXT CONTENT   </a>
		<a href="/index"> SOME    TEXT    CONTENT </a>

	**Example:** When `true` the following HTML strings are considered equal:

		<html>
			<title>
				This is my title
			</title>
		</html>

		<html><title>This is my title</title></html>


----------


- ####`verbose: {true|false}` default: **`false`**

    When `true`, instead of returning a boolean value  `CompareXML.equivalent?` returns an array of all errors encountered when performing a comparison.

	> **Warning:** When `true`, the comparison takes longer! Not only because more processing is required to produce meaningful error messages, but also because in this mode, comparison does **NOT** stop when a first error is encountered, because the goal is to capture as many discrepancies as possible.

	**Usage Example:** `CompareXML.equivalent?(doc1, doc2, {verbose: true})`

    **Example:** When `true` given the following HTML strings:

		<!DOCTYPE html>
		<html lang="en">
		<head><title>TITLE</title></head>
		<body>
			<h1>SOME HEADING</h1>
			<div id="content">
			    <h2><i class="fa fa-cogs"></i> ANOTHER HEADING</h2>
			    <p>Extra content</p>
			</div>
			<div class="window">
			    <a href="/admin" rel="icon">Link</a>
			</div>
			<blockquote>Some fancy quote <cite>Author Name</cite></blockquote>
			<p>Some more text</p>
			<p>Yet more text</p>
			<p>Too much text</p>
			<!-- The footer is below -->
			<p class="footer">FOOTER</p>
		</body>
		</html>

		<!DOCTYPE html>
		<html lang="en">
		<head><title>ANOTHER TITLE</title></head>
		<body>
			<h1 id="main">SOME HEADING</h1>
			<div id="content">
			    <h2><i class="fa fa-cogs"></i> ANOTHER HEADING</h2>
			    <p>Extra content</p>
			</div>
			<div class="window">
			    <a rel="button" href="/admin">Link</a>
			</div>
			<blockquote>Some fancy quote</blockquote>
			<p>Some more text</p>
			<p>Yet more text</p>
			<p>Too much text</p>
			<!-- This is the footer -->
			<div class="footer">FOOTER</div>
		</body>
		</html>

	`CompareXML.equivalent?(doc1, doc2, {verbose: true})` will produce an array shown below.

		[
			"html:head:title",
			"TITLE",
			10,
			"ANOTHER TITLE",
			"html:head:title"
		],
		[
		"html:body:h1",
			nil,
			2,
			"id=\"main\"",
			"html:body:h1"
		],
		[
		"html:body:div(2):a",
			"rel=\"button\"",
			4,
			"rel=\"icon\"",
			"html:body:div(2):a"
		],
		[
			"html:body:blockquote:cite",
			"cite",
			3,
			nil,
			"html:body:blockquote:cite"
		],
		[
			"html:body:p(4)",
			"p",
			8,
			"div",
			"html:body:div(3)"
		]

	The structure of the array is as follows:

	    [left_node_location, left_content, error_code, right_content, right_node_location]

	**Node location** of `html:body:p(4)` means that the element in question is `<p>`, its hierarchical ancestors are `html > body`, and it is the **4th** `<p>` tag. That is, it could be found in

        <html><body><p>one</p>...<p>two</p>...<p>three</p>...<p>TARGET</p></body></html>

	> **Note:** `p(4)` means that it is the fourth tag of type `<p>`, but there could be many other tags of other types between `p(3)` and `p(4)`.

	**Node content** displays the discrepancy in content (which could be the name of the tag, attributes, text content, comments, etc)

	**Error code** is a numeric value that indicates the type of a discrepancy. CompareXML implements the following error codes

	```ruby
    EQUIVALENT = 1              # nodes are equal (for internal use only)
    MISSING_ATTRIBUTE = 2       # attribute is missing its counterpart
    MISSING_NODE = 3            # node is missing its counterpart
    UNEQUAL_ATTRIBUTES = 4      # attributes are not equal
    UNEQUAL_COMMENTS = 5        # comment contents are not equal
    UNEQUAL_DOCUMENTS = 6       # document types are not equal
    UNEQUAL_ELEMENTS = 7        # nodes have the same type but are not equal
    UNEQUAL_NODES_TYPES = 8     # nodes do not have the same type
    UNEQUAL_TEXT_CONTENTS = 9   # text contents are not equal
	```

	Here is an example of how these could be used:

    ```ruby
    case error_code
      when CompareXML::UNEQUAL_ATTRIBUTES
        '!='
      when CompareXML::MISSING_ATTRIBUTE
        '?'
    end
    ```



## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request



## Credits

This gem was inspired by [Michael B. Klein](https://github.com/mbklein)'s gem [`equivalent-xml`](https://github.com/mbklein/equivalent-xml) - another excellent tool for XML comparison.



## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).