---
layout: post
title: Python Logging
comments: true
---

Python`s logging module is very versatile and very helpful for debugging issues in Software.This article examines some of the basic concepts of logging.

## Source Code Organization

There are 3 modules in the source code <a href="https://github.com/raghavan97/python_programs/blob/master/logging_example/module1" target="_blank">module1</a>, <a href="https://github.com/raghavan97/python_programs/blob/master/logging_example/module2" target="_blank">module2</a>, <a href="https://github.com/raghavan97/python_programs/blob/master/logging_example/module3" target="_blank">module3</a> . In main.py, we call functions from module1, module2. This would prove some of the concepts in logging module.

<img src="/images/logging-tree.png" alt="hi" class="inline" />

## Concepts

The root logger is configured by using logging.basicConfig call. We can specify the format of a log message and also the logging level. This configuration is done only once in the code. It is mandated to call basicConfig() before you make the first call using the logger.

{% highlight python %}
import logging

format_string = (
    '%(asctime)s:%(name)s:%(levelname)s:'
    '%(filename)s:%(lineno)d:%(funcName)s:%(module)s:%(message)s'
)
logging.basicConfig(format=format_string,level=logging.DEBUG)
{% endhighlight %}


After the root logger is configured , all the loggers would use the same format and logging level by default.

When you want to debug a module you might want to increase the logging level for that module. This is done by configuring the logger of that module. This is applicable only to that module.

{% highlight python %}
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.ERROR)
{% endhighlight %}

The <a href="https://github.com/raghavan97/python_programs/blob/master/logging_example/module3/main.py" target="_blank">main.py</a> provides the main entry point for the project. It initializes the *root* logger. It also has a few log messages to demonstrate the logging at different levels. It calls a function f1 in module m1 and a function f2 from m2.

## Program Execution and Output

The log messages indicate the time, severity, and also the origin of the message in terms of the source file and line number as follows 

<img src="/images/logging-messages.png" alt="hi" class="inline" />

{% highlight python %}
2017-07-01 15:40:15,929:module1.m1:CRITICAL:m1.py:9:f1:m1:This is CRITICAL message from m1,f1
{% endhighlight %}
