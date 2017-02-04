---
layout: post
title: Getter and Setter
---


Recently we had come across an issue in our project, where one of the instance variables in a class was found to be corrupted. 

It was hard tracking the assignment of value to the instance variable because 
* There were a number of places in the code base where the variable was getting assigned 
* Most of the assignments were from other variables in the code. 
    
The regular python debugger (pdb) does not have **watch point** support which could have simplified in catching the suspect. After googling around, it was not clear as to whether any of the python debuggers had the support for watch points.

We discussed a few ways of catching the culprit. The one that I am going to show you now caught my attention, since it was clean and smart.

## Problem

Here is the simulation of the same problem.

We have a class BankCustomer, which has 3 attributes *name*, *id* and *balance*. An instance of BankCustomer is created with an initial value of the *balance* as 10000. The value of balance has been changed many times to simulate the assignments in different modules.


{% highlight python %}
import inspect

class BankCustomer(object):
    def __init__(self, name, cust_id, balance):
        self.name = name
        self.cust_id = cust_id
        self._balance = balance


c1 = BankCustomer('Amit',1,10000)
c1.balance = 8000
c1.balance = 3000
c1.balance = 5000
c1.balance = 15000
c1.balance = 25000
c1.balance = 65000

{% endhighlight %}

The idea is to catch the place where an assignment of 5000 takes place.

## Solution

The way we solve this problem is 

* Make *balance* a property of the class
* In the property setter, we print the caller branching on the condition of interest

We make use of the inspect module **stack()** call to get the caller information.

The following shows the modified code for solving the issue.

{% highlight python %}
import inspect

class BankCustomer(object):
    def __init__(self, name, cust_id, balance):
        self.name = name
        self.cust_id = cust_id
        self._balance = balance

    @property
    def balance(self):
        return self._balance

    @balance.setter
    def balance(self, value):
        if self._balance == 5000:
            frame, filename, line_num, func, source_code, source_index = inspect.stack()[1]
            code = source_code[source_index].strip()
            msg = 'balance=5000 at {}:{} code="{}"'.format(filename,line_num, code)
            print(msg)
        self._balance = value


c1 = BankCustomer('Amit',1,10000)
c1.balance = 8000
c1.balance = 3000
c1.balance = 5000
c1.balance = 15000
c1.balance = 25000
c1.balance = 65000
{% endhighlight %}

We run the example. We can see a print that identifies the culprit. The print shows the next executable statement after the desired assignment.

{% terminal %}
$ python example.py
balance=5000 at try.py:27 code="c1.balance = 15000"
{% endterminal %}
