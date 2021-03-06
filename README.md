A pynt of Python build.
=============================

[**Raghunandan Rao**](https://github.com/rags)

## Features

* Easy to learn.
* Build tasks are just python funtions.
* Manages dependancies between tasks.
* Automatically generates a command line interface.
* Rake style param passing to tasks

## Installation


You can install pynt from the Python Package Index (PyPI) or from source.

Using pip

```bash
$ pip install pynt
```

Using easy_install

```bash
$ easy_install pynt
```

## Example


The build script is written in pure Python and pynt takes care of managing
any dependancies between tasks and generating a command line interface.

Writing build tasks is really simple, all you need to know is the @task decorator. Tasks are just regular Python functions marked with the ``@task()`` decorator. Dependancies
are specified with ``@task()`` too. Tasks can be ignored with the ``task(ignore=True)``.

**build.py**
------------

```python

#!/usr/bin/python

import sys
from pynt import task

@task()
def clean():
    '''Clean build directory.'''
    print 'Cleaning build directory...'

@task(clean)
def html(target='.'):
    '''Generate HTML.'''
    print 'Generating HTML in directory "%s"' %  target

@task(clean, ignore=True)
def images():
    '''Prepare images.'''
    print 'Preparing images...'

@task(html,images)
def start_server(server='localhost', port = '80'):
    '''Start the server'''
    print 'Starting server at %s:%s' % (server, port)

@task(start_server) #Depends on task with all optional params
def stop_server():
    print 'Stopping server....'

@task()
def copy_file(src, dest):
    print 'Copying from %s to %s' % (src, dest)

@task()
def echo(*args,**kwargs):
    print args
    print kwargs

```

**Running pynt tasks**
-----------------------

The command line interface and help is automatically generated. Task descriptions
are extracted from function docstrings.

```bash    
$ pynt
usage: b [-h] [-l] [-f file] [task [task ...]]

positional arguments:
  task                  perform specified task and all it's dependancies

optional arguments:
  -h, --help            show this help message and exit
  -l, --list-tasks      List the tasks
  -f file, --file file  Build file to read the tasks from. Default is
                        'build.py'

Tasks in build file ./build.py:
  clean                       Clean build directory.
  copy_file                   
  echo                        
  html                        Generate HTML.
  images           [Ignored]  Prepare images.
  start_server                Start the server
  stop_server                 

Powered by pynt - A Lightweight Python Build Tool.
```
          
pynt takes care of dependencies between tasks. In the following case start_server depends on clean, html and image generation (image task is ignored).

```bash
$ pynt start_server
[ example.py - Starting task "clean" ]
Cleaning build directory...
[ example.py - Completed task "clean" ]
[ example.py - Starting task "html" ]
Generating HTML in directory "."
[ example.py - Completed task "html" ]
[ example.py - Ignoring task "images" ]
[ example.py - Starting task "start_server" ]
Starting server at localhost:80
[ example.py - Completed task "start_server" ]
```

The first few characters of the task name is enough to execute the task, as long as the partial name is unambigious. You can specify multiple tasks to run in the commandline. Again the dependencies are taken taken care of.

```bash
$ pynt cle ht cl
[ example.py - Starting task "clean" ]
Cleaning build directory...
[ example.py - Completed task "clean" ]
[ example.py - Starting task "html" ]
Generating HTML in directory "."
[ example.py - Completed task "html" ]
[ example.py - Starting task "clean" ]
Cleaning build directory...
[ example.py - Completed task "clean" ]
```

The 'html' task dependency 'clean' is run only once. But clean can be explicitly run again later.

pynt tasks can accept parameters from commandline.

```bash
$ pynt "copy_file[/path/to/foo, path_to_bar]"
[ example.py - Starting task "clean" ]
Cleaning build directory...
[ example.py - Completed task "clean" ]
[ example.py - Starting task "copy_file" ]
Copying from /path/to/foo to path_to_bar
[ example.py - Completed task "copy_file" ]
```

pynt can also accept keyword arguments.

```bash
$ pynt start[port=8888]
[ example.py - Starting task "clean" ]
Cleaning build directory...
[ example.py - Completed task "clean" ]
[ example.py - Starting task "html" ]
Generating HTML in directory "."
[ example.py - Completed task "html" ]
[ example.py - Ignoring task "images" ]
[ example.py - Starting task "start_server" ]
Starting server at localhost:8888
[ example.py - Completed task "start_server" ]
    
$ pynt echo[hello,world,foo=bar,blah=123]
[ example.py - Starting task "echo" ]
('hello', 'world')
{'blah': '123', 'foo': 'bar'}
[ example.py - Completed task "echo" ]
```

**Organizing build scripts**
-----------------------------

You can break up your build files into modules and simple import them into your main build file.

```python
from deploy_tasks import *
from test_tasks import functional_tests, report_coverage
```

## Contributors/Contributing


* Calum J. Eadie - pynt is preceded by and forked from [microbuild](https://github.com/CalumJEadie/microbuild), which was created by [Calum J. Eadie](https://github.com/CalumJEadie).


If you want to make changes the repo is at https://github.com/rags/pynt. You will need [pytest](http://www.pytest.org) to run the tests

```bash
$ ./b t
```

It will be great if you can raise a [pull request](https://help.github.com/articles/using-pull-requests) once you are done.

*If you find any bugs or need new features please raise a ticket in the [issues section](https://github.com/rags/pynt/issues) of the github repo.*
    
## License

pynt is licensed under a [MIT license](http://opensource.org/licenses/MIT)
