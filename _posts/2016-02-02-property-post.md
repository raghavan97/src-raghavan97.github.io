---
layout: post
title: Property in Python Class
comments: true
abs_url: https://raghavan97.github.io/2016/02/02/property-post
disqus_id: c738c80b-caf2-4b6f-9f99-f30b739976f2
---


Recently we came across an interesting issue in our python project.

A class was instantiated with a particular value given to a instance variable. A few user actions later, an unexpected value of the instance variable was found. The symptoms ended there.

An initial attempt of trying to track the assignment of instance variable by code inspection was getting hard because
* There were a number of places in the code base where the variable was getting assigned 
* Most of the assignments were from other variables in the code. 
    
We looked for <a href="https://sourceware.org/gdb/onlinedocs/gdb/Set-Watchpoints.html">watch point</a> support in the python debugger (pdb) that could have simplified in catching the suspect. Unfortunately, pdb did not have watch point support. We did a little bit of googling around, to find other python debuggers with watch point support. The search did not yield any familiar python debuggers having watch point support.

We discussed a few ways of catching the culprit. The one that I am going to show you now caught my attention, since it was clean and smart.

## Problem

Here is the simulation of the same problem in a small piece of code.

We have a class **BankCustomer**, which has 3 attributes *name*, *id* and *balance*. An instance of BankCustomer is created with an initial value of the *balance* as 10000. The value of balance has been changed many times to simulate the assignments in different modules.


{% highlight python %}
import inspect

class BankCustomer(object):
    def __init__(self, name, cust_id, balance):
        self.name = name
        self.cust_id = cust_id
        self.balance = balance


c1 = BankCustomer('Amit',1,10000)
c1.balance = 8000
c1.balance = 3000
c1.balance = 5000
c1.balance = 15000
c1.balance = 25000
c1.balance = 65000

{% endhighlight %}

The idea is to catch the place where an assignment of a specific unacceptable value takes place. In this example, 5000 is an unacceptable value for balance.

## Solution

The way we solve this problem is 

* Make *balance* a property of the class
* In the property setter, we print the caller branching on the condition of interest

We make use of the <a href="https://docs.python.org/2/library/inspect.html">inspect</a> module **stack()** call to get the caller information.

The following shows the modified code for solving the issue.

<script src="https://gist.github.com/raghavan97/a750f606401a354299b13538c29737fa.js"></script>

We run the example. We can see a print that identifies the culprit. The print shows the *next executable statement* after the unacceptable assignment.

{% terminal %}
$ python try.py
assigned at try.py:38 code="c1.balance = 15000"
{% endterminal %}
