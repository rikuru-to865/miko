# Quick Start
## Template Manager
You can use the `Manager` class to prepare templates easily, and render them easily.  
So, when you use it in the backend of a web service, you should define the `Manager` class first.
```python
from miko import Manager

manager = Manager()
```

## Syntax
In miko, the part of a template enclosed in a two-lettered hat caret is a function and is executed in Python.  
This enclosed area is called a block in miko.  
The block is replaced by the value returned by the block.  
In addition, the last line of the block is automatically replaced by `return` even if you don't write `return`.  
If you don't know, it is faster to look at an example.
### Example
#### html
```html
<title>^^ title ^^</title>
<!-- The one above is equivalent to the one below.
  `<title>^^ return title ^^</title>` -->
```
#### python
```python
manager.render("template.html", title="Hi user guide!")
```
#### Result html
```html
<title>Hi user guide!</title>
```

Of course, you can also do the following:
#### html
```html
<ul>
  ^^ "".join(f"<li>{name}</li>" for name in members) ^^
</ul>
```
#### python
```python
manager.render("template.html", members=("tasuren", "yuki", "kumi"))
```

## Builtins
A built-in is a variable that can be used from the beginning in a template block.  
There are functions and so on.
* `Template` class instance: `self`
* Insert another file: `include`
  (`miko.builtins.include`)
* Truncate text: `truncate`
  (`miko.builtins.truncate`)
* Escape text: `escape` (`html.escape`)
* A constant containing a two-letter cat-halet string: `CS`
  (`miko.builtins.CS`)
* `Manager` class instance: `manager` (If you used the Manager class for rendering.)

miko is still waiting for a PullRequest in the GitHub repository as there are not many yet.

## Inheriting Templates
In miko, you can inherit templates by using the attribute `render` of the instance of the `Manager` class that is automatically passed to the block.  
(Or you can use the extends attribute of an instance of the Template class.)
### Examples
#### base.html
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>miko's website - ^^ title ^^</title>
</head>
<body>
  ^^ main ^^
</body>
</html>
```
#### page1.html
```html
^^
  manager.render(
      "base.html", title="miko's first page",
      main="""
        <h1>I did it!</h1>
        I made my first webpage.
      """
  )
  # or self.extends("base.html", ...)
^^
```

## Make alias for Inheriting Templates
By using the extends argument and attributes of the ``Manager`` class, it is possible to create an alias for ``manager.render`` that inherits from the template.
### Examples
#### Python
```python
manager = Manager()

def blog(title, main, header=""):
    return manager.render("base.html", title=title, main=main, header="")

manager.extends["blog"] = blog
```
#### HTML
```html
^^
  self.blog(
    "title", """
    <h1>Main content</h1>
    """,
    "<script ...></script>"
  )
  # Aliase for `manager.render(title="title", ...)`
^^
```

## Escape
If you place the user name and description fields in the template, it is better to escape them to prevent XSS injection.  
If you are going to do that escaping, you should do the following:
```html
<div class="description">
  ^^ escape(user.description) ^^
</div>
```
Yes, you can use the built-in `escape` function.  
The `escape` function is a function of `html`, the Python standard library.  
### Notes
If you want to automate this escaping, you can use the `adjustors` argument of the constructor of the `Template` class.  
(If you are using `Manager`, pass it to `Manager`.)

## Share variables with other blocks
The template class :class:`miko.template.Template`, an instance of ``self``, is available in templates.  
By adding a new attribute to this ``self``, you can carry the value over to another block in the template.
```html
^^ self.BASE_URL = "https://tasuren.f5.si/"; "" # Return null text ^^

...

<a href="^^ f'{self.BASE_URL}portfolio' ^^">Go to portfolio.</a>
```

## Asynchronous Support
miko has good asynchronous support.  
There is an ``aiorender`` method, so use it.  
You can also use ``await`` in templates that render asynchronously to allow other asynchronous functions to be rendered asynchronously.
```html
^^ self.user = await self.app.fetch_user(user_id) ^^

<h1>^^ escape(user.name) ^^'s profile</h1>
<div class="details">^^ escape(user.details) ^^</div>
```

## About the name miko
That it is not pronounced "maiko".  
A miko (巫女 - sibyl) is a woman who serves the Japanese gods and is found in jinja (神社 - shrines).  
I named it miko to make it look like another template engine choice for jinja, Python's famous template engine.  
If you want to see what a shrine maiden looks like, search for `巫女`.  
(If you're an anime fan, you may know this.)