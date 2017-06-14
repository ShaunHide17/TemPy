# TemPy
### Html templating with no html involved!

### What
Build HTML without writing a single tag.
TemPy lets you dynamically generate HTML and accessing it in pure Python or in a jQuery fashion. Also "navigating the DOM" and manipulating tags is possible in a Python or jQuery friendly sintax.

### Why?
HTML is like SQL: we all use it, we know it works, we all recognize it's important, but our biggest dream is to never use it anymore. For SQL we have ORM's, for HTML we're not there yet.
Templating systems are cool (Python syntax in html code) but not cool enough (you still have to write html somehow)..
..so the idea of TemPy.

## Build, manipulate, and navigate HTML documents. With no HTML involved.


# Usage:


## Basic Templating

TemPy offers a rather clean syntax for building pages in pure python:
```
my_text_list = ['This is foo', 'This is Bar', 'Have you met my friend Baz?']
page = Html()(
    Head()(
        Meta(charset='utf-8'),
        Link(href="my.css", typ="text/css", rel="stylesheet")
    ),
    Body()(
        Div(klass='linkBox')(
            A(href='www.foo.bar')
        ),
        (P()(my_text_list[i]) for i in range(2))
    )
).render(pretty=True)
>>> <html>
>>>     <head>
>>>         <meta charset="utf-8"/>
>>>         <link href="my.css" type="text/css" rel="stylesheet"/>
>>>     </head>
>>>     <body>
>>>         <div class="linkBox">
>>>             <a href="www.foo.bar"></a>
>>>         </div>
>>>         <p>This is foo</p>
>>>         <p>This is Bar</p>
>>>         <p>Have you met my friend Baz?</p>
>>>     </body>
>>> </html>
```

You can also create blocks and put them togheter using the manipulation api:
```
# basic_template.py
from somewhere import links, foot_imgs
menu = Div(klass='menu')(Li()(A(href=link)) for link in links)
footer = Div(klass='coolFooterClass')(Img(src=img) for img in foot_imgs)
page = Html()(Head(), Body()(header, menu, container=Div(klass='container'), footer=footer))
...
# my_controller.py
from tempy.tags import Div
from basic_template import page
@controller_framework_decorator
def mycontroller():
    content = Div()('This is my content!')
    return page.container.append(content)
```

## Elements creation and removal
You can create a DOM elements instantiating tags:
```
page = Html()
>>> <html></html>
```

You can then add elements or content calling them (just like a function)...
```
page(Head())
>>> <html><head></head></html>
```
..or you can use one of the jQuery-like apis:
```
body = Body()
page.append(body)
>>> <html><head></head><body></body></html>

div = Div().append_to(body)
>>> <html><head></head><body><div></div></body></html>
div.append('This is some content', Br(), 'And some Other')
>>> <html><head></head><body><div>This is some content<br>And some Other</div></body></html>
```
..same for removing:
```
head.remove()
>>> <html><body><div></div></body></html>
body.empty()
>>> <html><body></body></html>
page.pop()
>>> <html></html>
```

All the main jQuery manipulating apis are provided.


## Attributes attribuition 
You can add attributes to every element at definition time or later:
```
div = Div(id='my_html_id', klass='someHtmlClass') # 'klass' is 'class' but without overriding Python's buildin keywords
>>> <div id="my_dom_id" class="someHtmlClass"></div>

a = A(klass='someHtmlClass')('text of this link')
a.attr(id='another_dom_id')
a.attr({'href': 'www.thisisalink.com'})
>>> <a id="another_dom_id" class="someHtmlClass" href="www.thisisalink.com">text of this link</a>
```

Also style is editable in the jQuery fashion:
```
div2.css(width='100px', float='left')
div2.css({'height': '100em'})
div2.css('background-color', 'blue')
>>> <div id="another_dom_id" class="someHtmlClass comeOtherClass" style="width: 100px; float: left; height: 100em; background-color: blue"></div>
```

## "Navigating the DOM"

Every TemPy Tag content is iterable and accessible just like a Python list:
```
divs = [Div(id=div, klass='inner') for div in range(10)]
ps = (P() for _ in range(10))
container_div = Div()(divs)

for i, div in enumerate(container_div):
    div.attr(id='divId'+i)
container_div[0].append(ps)
container_div[0][4].attr(id='pId')
>>> <div>
>>>     <div id="divId0">
>>>         <p></p>
>>>         <p></p>
>>>         <p></p>
>>>         <p></p>
>>>         <p id="uniquePid"></p>
>>>         <p></p>
>>>         <p></p>
>>>         <p></p>
>>>         <p></p>
>>>         <p></p>
>>>     </div>
>>>     <div id="divId1"></div>
>>>     <div id="divId2"></div>
>>>     <div id="divId3"></div>
>>>     <div id="divId4"></div>
>>>     <div id="divId5"></div>
>>>     <div id="divId6"></div>
>>>     <div id="divId7"></div>
>>>     <div id="divId8"></div>
>>>     <div id="divId9"></div>
>>> </div>
```

..or if you give a name to the element when adding it to his container you can access it by name, as if it's a container's attribute:
```
container_div = Div()
container_div(content_div=Div())

container_div.content_div('Some content')
>>> <div><div>Some content</div></div>
```


..or if you feel jQuery-ish you can use:
```
container_div.children()
container_div.first()
container_div.last()
container_div.next()
container_div.prev()
container_div.prev_all()
container_div.parent()
container_div.slice()
```

## Made and Mantained by Federico Cerchiari / Hrabal
### Contribute.
Any contribution is welcome. Fork and PR if you have some ideas you want to code in.
PM me if you want to help maintaining or coding.
Next steps of development:
- Implement math operators for DOMElement i.e: Div() += Div() add the latter as a child.
- Writing more tests.
- Improve .find method.
- Pretty print
- To make everything an xml and use lxml.cssselect or to not change everything, here's the dilemma.
- New module: "css builder" extracts the style properties from content, and creates the css file with the correct selector.
- Cache for css builder module
- Any suggestion?
### Apache 2.0 license, see LICENSE for details.
