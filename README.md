tornado-rest-handler
====================

![Continuous Integration Status](https://secure.travis-ci.org/paulocheque/tornado-rest-handler.png)

A simple Python Tornado handler that manage Rest requests automatically.

* [Basic Example of Usage](#basic-example-of-usage)
  * [Routes](#routes)
  * [Handlers](#handlers)
  * [Templates](#templates)
* [Installation](#installation)
* [Change Log](#change-log)
* [TODO](#todo)

Basic Example of Usage
------------------------

In the current implementation, there is only one handler for MongoEngine ORM, besides the library does not depends on the MongoEngine!

With +-10 lines of code you can create a handler for your ORM.

Routes
------------------------

One handler manage every Rest routes:

| Method       | Route               | Comment |
|------------- |---------------------|---------|
| GET          | /animal index       | display a list of all animals |
| GET          | /animal/new         | new return an HTML form for creating a new animal |
| POST         | /animal             | create a new animal |
| GET          | /animal/:id         | show an animal |
| GET          | /animal/:id/edit    | return an HTML form for editing a photo |
| PUT          | /animal/:id         | update an animal data |
| DELETE       | /animal/:id         | delete an animal |
| POST*        | /animals/:id/delete | same as DELETE /animals/:id |
| POST*        | /animals/:id        | same as PUT /animals/:id |

* *Since HTML5-forms does not support PUT/DELETE. It is possible to use the following methods too:


```python
from tornado_rest_handler import routes, rest_routes

TORNADO_ROUTES = [
    # another handlers here

    rest_routes(Animal),

    # another handlers here
]

TORNADO_SETTINGS = {}

application = tornado.web.Application(routes(TORNADO_ROUTES), **TORNADO_SETTINGS)
```

The library does not support auto-plurazation yet, so you may want to change the prefix:

```python
rest_routes(Animal, prefix='animals'),
```

You can also define to where will be redirect after an action succeed:

```python
rest_routes(Animal, prefix='animals', redirect_pos_action='/animals'),
```

Handlers
------------------------

All the get/post/put/delete methods are implemented for you, but if you want to customize some behavior, you write your own handler:

```python
class AnimalHandler(tornado.web.RequestHandler):
    pass # your custom methods here
```

And then, registered it:

```python
rest_routes(Animal, handler=AnimalHandler),
```

To create a RestHandler for your ORM you must override the RestHandler class and implement the following methods:

```python
from tornado_rest_handler import RestHandler

class CouchDBRestHandler(RestHandler):
    def instance_list(self): return [] # it can return a list or a queryset etc
    def find_instance_by_id(self, obj_id): pass
    def save_instance(self, obj): pass
    def update_instance(self, obj): pass
    def delete_instance(self, obj): pass
```

By default, the list page will show all models of that type. To filter by user or other properties, override the *instance_list* method:

```python
class AnimalHandler(tornado.web.RequestHandler):
    def instance_list(self):
        return Animal.objects.filter(...)
```


Templates
------------------------

You must create your own template. Templates will receive the variables **obj** or **objs** and **alert** in case there is some message. The edit template will also receive the variable **errors** and functions **value_for**, **error_for** and **has_error**.

It must have the names list.html, show.html and edit.html. But you can customize if you want to:

```python
rest_routes(Animal, list_template='another_name.html', edit_template='...', show_template='...'),
```

By default, the directory is the model name in lower case (animal in this example).

* animal/list.html
* animal/show.html
* animal/edit.html

But you may change the directory though:

```python
rest_routes(Animal, template_path='your_template_path'),
```


Installation
------------

```
pip install tornado-rest-handler
```

#### or

```
1. Download zip file
2. Extract it
3. Execute in the extracted directory: python setup.py install
```

#### Development version

```
pip install -e git+git@github.com:paulocheque/tornado-rest-handler.git#egg=tornado-rest-handler
```

#### requirements.txt

```
tornado-rest-handler==0.0.4
# or use the development version
git+git://github.com/paulocheque/tornado-rest-handler.git#egg=tornado-rest-handler
```

#### Upgrade:

```
pip install tornado-rest-handler --upgrade --no-deps
```

#### Requirements

* Python 2.6 or 2.7
* Tested with Tornado 2.4.1


Change Log
-------------

#### 0.0.4 (2013/03/31)
* [new] Passed validation errors (dict errors) to the edit template.
* [new] New functions passed to edit template: has_errors, value_for and error_for.
* [bugfix] Fixed bug that cause strange behavior when occurs validation errors.

#### 0.0.3 (2013/03/31)
* [new] CrudHandler extracted from RestHandler.
* [new] Dynamic handlers with dynamic routes (rest_routes function).
* [new] New redirect_pos_action attribute.
* [new] Function routes added to facilitate routes integration.
* [new] Method raise403 useful method in the handler.
* [update] All attributes are now in lower case.
* [update] Stronger uri discover algoritihm.
* [update] Using only AssertionError exceptions.
* [bugfix] Using redirects instead of rendering after actions.


#### 0.0.2 (2013/03/30)
* [update] RestHandler adapted to be used for other ORMs.
* [new] MongoEngineRestHandler
* [new] Template customization: LIST_TEMPLATE, EDIT_TEMPLATE, SHOW_TEMPLATE variables.
* [update] Using OO instead of metaclasses for object list.
* [update] Better exception to alert bad implementations.
* [tests] Initial unit tests.

#### 0.0.1 (2013/03/30)

* [new] RestHandler for MongoEngine


TODO
-------------

* Handlers for another ORMs (other than MongoEngine).
* Pagination
* i18n
* Use fields and exclude to facilitate auto-generate forms:
* plurarize
* splitted handlers
