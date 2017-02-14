---
layout: post
title: Python Application Directory Structure
---
One of the things that we need to get right for a python application is the way the project files are organized.  


A correct directory structure makes it easier for
* importing modules within the project
* logging messages in the logfile more meaningfully
* packaging and installation

This blog is about a directory structure that works well on the above counts.

## Project Structure

A project can consist of multiple python modules. Each module might have one or more python source files. A sample project for demonstrating the proposed directory structure is shown below as the output of **tree** command.

<img src="/images/dir.png" alt="hi" class="inline" />

The project contains the following directories and files

* The sources of the project are under a directory *my_proj*. Incidentally *my_proj* is also a module.
* There are 2 modules *m1* and *m2* within *my_proj* that contain a source file each.
* A *setup.py* that helps create a pip installable package is at the root of directory tree
* A script *my_app* residing at the root of directory tree. *my_app* comes up in the PATH of the user after pip install for starting the application.

The <a href="https://github.com/raghavan97/projdir/blob/master/my_proj/main.py">main.py</a> provides the main entry point for the project. It initializes the *root* logger. It also has a few log messages to demonstrate the logging at different levels. It calls a function in module m1. The directory structure helps the importing of function in any of the modules using project name, module name and source file name as follows 

{% highlight python %}
from my_proj.m1.m1_src import m1f
{% endhighlight %}

The <a href="https://github.com/raghavan97/projdir/blob/master/my_proj/m1/m1_src.py">m1-src.py</a> shows how we can change the logging level in any of the source file and log messages. There is a call to a function *m2f* from a different module *m2* by using the import statement as explained above.


In <a href="https://github.com/raghavan97/projdir/blob/master/myapp">myapp</a> , we have a script to start the application. Observe that it does not have an extension in the file name. It merely imports the main entry point from *my_proj/main*  and invokes it. The import statement follows the same convention as explained above.


In <a href="https://github.com/raghavan97/projdir/blob/master/setup.py">setup.py</a> we specify *my_app* as the script that starts the application. After the package is pip installed *my_app* comes up in the PATH and can be used to start the application. In setup.py , we also specify all the packages in the project *my_proj*, *my_proj.m1* and *my_proj.m2*


## Build, Install and Execute

Here is how we build the package, install it and run the app.


<img src="/images/packaging.png" alt="hi" class="inline" />

The log messages indicate the time, severity, and also the origin of the message in terms of the source file and line number as follows 
{% highlight python %}
2017-02-14 07:05:42,091:CRITICAL:my_proj.m1.m1_src:8:[message content]
{% endhighlight %}

The source file, *my_proj.m1.m1_src* is indicated in the regular python dotted notation of module and source file. The line number *8* is the next field in the log message.

