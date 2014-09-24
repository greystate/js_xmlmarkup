# XmlMarkup

-- Javascript object to help build XML and XHTML markup at runtime.

Sample usage (assuming jQuery or something like that):

```javascript
xml = new XmlMarkup();
xml.tag('ul' function() {
  xml.tag('li', 'item one');
  xml.tag('li', 'item two');
});

$('#list-placeholder').html(xml.target());
```

Note use of the `target()` function to retrieve the marked up text at the end.

## More detailed information...

### function *tag(tagname, optionalArguments)*

*optionalArguments* can be:

- omitted, in which case it's a simple tag
- an object (hash in Ruby), in which case, it's a name/value set of attributes
- a string, in which case, it is interpreted as the contents of the tag
- a function, in which case, it is a set of further instructions lexically
  one level deeper (i.e., a tag enclosed in another tag).

## Examples:

### Simple tag

```javascript
xml.tag('personlist') => <personlist/>
```

### Content tag

```javascript
xml.tag('person', 'bob') => <person>bob</person>
```

### Nested tags

```javascript
xml.tag('personlist', function() { xml.tag('person', 'bob')}) => <personlist><person>bob</person></personlist>
```

### Tag with attributes

```javascript
xml.tag('person', {name: 'bob'}) => <person name="bob">
```

*Warning: You cannot use reserved words like "class" unless you stringify them, e.g.:*

```javascript
xml.tag('ul', {'class': 'menu'}, function() { etc. });
```

### Arbitrary text anyplace

```javascript
xml.tag('th', function() {
  xml.text('column sorting ');
  xml.tag('img', {src: 'images/spacer.gif', 'class': 'sortdown'});
});
```

#### Result:

	<th>
	  column sorting <img src="images/spacer.gif" class="sortdown"/>
	</th>

### Tag with multiple contained things (complicated example)

```javascript
xml.tag('table', function() {
  xml.tag('thead', function() {
    xml.tag('tr', function() {
      xml.tag('th', 'earth');
      xml.tag('th', 'wind');
      xml.tag('th', 'fire');
    });
  });
  xml.tag('tbody', function() {
    elements = Element.all();
    for(element in elements) {
      xml.tag('td', elements[element].earth);
      xml.tag('td', elements[element].wind);
      xml.tag('td', elements[element].fire);
    }
  });
});
```

#### Result:

	<table>
	  <thead>
	    <tr>
	      <td>earth</td>
	      <td>wind</td>
	      <td>fire</td>
	    </tr>
	  </thead>
	  <tbody>
	    <tr>
	      <td>earth 1</td>
	      <td>wind 1</td>
	      <td>fire 1</td>
	    </tr>
	    <tr>
	      ...etc...
	    </tr>
	  </tbody>
	</table>

Extracting the generated markup is as easy as: `xml.target()`.
You are encouraged to read the tests for a pretty good overview
of what this package does.

This code is inspired by the **Builder::XmlMarkup** code (originally
by Jim Weirich). Because JavaScript cannot create magic methods,
one method steps in to take the place of many. That is "tag".
Here is a comparison from the last example between the Ruby
implementation and this one:

```ruby
xml = Builder::XmlMarkup.new
xml.table {
  xml.thead {
    xml.tr {
      xml.th 'earth'
      xml.th 'wind'
      xml.th 'fire'
    }
  }
  xml.tbody {
    elements = Element.all # assume ActiveRecord or Datamapper or whatever model "Element"
    elements.each |element| {
      xml.tr {
        xml.td element.earth
        xml.td element.wind
        xml.td element.fire
      }
    }
  }
}
```

Some attempts have been made to protect you from yourself (like HTML-escaping strings)
and attributes. However, you are still free to shoot yourself in the foot, and it is
easy to generate invalid markup. You still need to think before coding. Sorry.

License: [MIT](http://www.opensource.org/licenses/mit-license.html)
