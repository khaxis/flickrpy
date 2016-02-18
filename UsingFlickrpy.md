_**Notice:** This article is INCOMPLETE._

# Introduction #

This document assumes 2 things about you. You are aware of what the Flickr API is, along with what it does, and have a basic understanding of Python.

If you are unsure of what a "basic understanding of Python is", basically if you know how to use imports, how to run methods within a class (though if unsure about that one, continue reading and you shall learn) and how to use variables, you are set.


# Take a Look at the Source #

## Your API KEY ##

The great thing with open source, and Python for that matter (because Python is mostly open source), is that you can easily look at the source (obviously!). This is **the** best learning tool when it comes to learning how something works: Look at how it works. Look at what it does.

If you have flickr.py open while reading this, that will help you understand what is being said.

The first thing you will notice, that is important to you, is the variable `API_KEY`. Look out for the comment `#set these here or using flickr.API_KEY in your application`. If you do not already have an Flickr API KEY, it would be a [good idea to get one](http://www.flickr.com/services/api/). It should be obvious what you need to do here, fill in your API KEY. An example is as follows (the API KEY in the example is a Flickr example key. Don't try and use it):

```
#set these here or using flickr.API_KEY in your application
API_KEY = '9a0554259914a86fb9e7eb014e4e5d52'
email = None
password = None
```

Your API KEY is important, as flickr.py won't work without it!


## import flickr ##

You won't be doing any of your programming/scripting within flickr.py, unless you really want to, but it can get really ugly very quickly if you try and do everything in the same document (basically, you only edit flickr.py if you need to add more methods, or unless you know what you are doing, but for this tutorial, it is best if we just let everything other than the API\_KEY variable be). To use the flickr.py functions, you will need to "import" the flickr.py file. To do this, at the top of the python file you will be working with (lets just assume the name **main.py** for now, you will need to do:

`import flickr`

First things first: It is important that **main.py** is in the same folder as **flickr.py**. If you wish to change this, you will need to alter the import statement, but for this tutorial, we are going to assume that all the files are in the same directory unless stated otherwise.

Secondly: If you have never imported another Python file from the same directory, you may wonder why the code **is not** `import flickr.py**. Simple: It is the way it was designed. When you import a Python file, it will compile that file into a .pyc file, or**flickr.pyc**file for to make it more efficient, and if**flickr.py**is not in the folder but**flickr.pyc**is, it will call that instead. It just makes it less complicated. Just think the name you import equals the name of the file, without the .py.**

Lets have a quick test of this import feature, and running a function within flickr.py from main.py. If this doesn't make sense to you, it will be explained in the next section.

**main.py**
```
import flickr

list = flickr.favorites_getPublicList(user_id)
print list
```

In this example, you will need to add any user\_id replacing the `user_id` text (don't forget quotation marks).


## Understanding Each Method/Function ##

Firstly, lets clarify what a "method" is and what a "function" is. A function is something that starts with `def` basically. If you are still confused, [do a Google search](http://www.google.com/search?q=what+is+a+function+in+python). A method is a a function within a class. Again, if you are still unsure, [do a Google search](http://www.google.com/search?q=what+is+a+method+in+python)!