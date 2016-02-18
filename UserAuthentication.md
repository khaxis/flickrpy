# Introduction #

User authentication allows you to do things with users accounts, for example, access private photos for use within the program that is being built. Flickr has many steps involved in Authentication within programs. This page will explain how to use authentication within flickr.py.


# Reading Material #

Flickr has a page explaining [user authentication](http://www.flickr.com/services/api/misc.userauth.html). It will help you if you read that page in full before continuing on this page.


# Modifications to flickr.py #

_**Notice:** These modifications are now included by default in flickr.py SVN [revision 7](https://code.google.com/p/flickrpy/source/detail?r=7). Please skip this section (though you can still read it to help understand the implementation)_

~~Currently, the user authentication methods/functions are not included with the flickr.py download~~, as they need a bit of cleaning up before they can be used, but they do work. Add the following to the bottom of the flickr.py document, before the line `if __name__ == '__main__':`.

```
class Auth():
    def getFrob(self):
        """Returns a frob that is used in authentication"""
        method = 'flickr.auth.getFrob'
        signature_string = hashlib.md5(API_SECRET + 'api_key' + API_KEY + 'method' + method).hexdigest()
        data = _doget(method, auth=False, api_sig=signature_string)
        return data.rsp.frob.text

    def loginLink(self, permission, frob):
        """Generates a link that the user should be sent to"""
        myAuth = Auth()
        signature_api = hashlib.md5(API_SECRET + 'api_key' + API_KEY + 'frob' + frob + 'perms' + permission).hexdigest()
        perms = permission
        link = "http://flickr.com/services/auth/?api_key=" + API_KEY+ "&perms=" + perms + "&frob=" + frob + "&api_sig=" + signature_api
        return link

    def getToken(self, frob):
        """This token is what needs to be used in future API calls"""
        method = 'flickr.auth.getToken'
        signature_token = hashlib.md5(API_SECRET + 'api_key' + API_KEY + 'frob' + frob + 'method' + method).hexdigest()
        data = _doget(method, auth=False, api_sig=signature_token, api_key=API_KEY, frob=frob)
        return data.rsp.auth.token.text
```

Along with this modification, you will need to add a variable called `API_SECRET` under the variable `API_KEY`. The variable has been added to the following example:

```
#set these here or using flickr.API_KEY in your application
API_KEY = '[your api key]'
*API_SECRET = '[your api secret]'*
email = None
password = None
```

Your API secret code is generated along with API\_KEY, and can be found on your Flickr [page of api keys](http://www.flickr.com/services/api/keys/). Make sure you are logged in with the account you generated they key with when accessing that page.

# How to Authenticate User #

Using these methods do require a bit in the code you are writing. In the following example of how to use the authentication class and methods, we will assume the name of "main.py", and it is in the same directory of "flickr.py".

**main.py**
```
## Import required files for this example.

import flickr,os

## The following line is a variable that holds what permission
## your program is requesting.
##   - "read"
##   - "write" (which allows read as well)
##   - "delete" (which allows read and write as well)
## are the 3 options available to you.

permission = "read"


## The following line links the class Auth() into a variable
## so that the methods within can be used.

myAuth = flickr.Auth()


## The following line gets a "frob". The frob will change
## each time you call it, and it needs to be the same
## throughout the authentication process, so once it is
## put into a variable, that variable will be used in future.

frob = myAuth.getFrob()


## The following line generates a link that the user needs
## to be sent to. The user must be logged into Flickr in the
## browser that is opened, and it will ask if it wishes to
## allow your program to access it. Your API KEY must be
## setup correctly within Flickr for this to work. See
## below if you have any problems.
##
## The permissions and frob variable are passed onto
## the link method.

link = myAuth.loginLink(permission,frob)


## The following line just lets the user know they need
## to be logged in, and gives them a chance to before
## the rest of the script is processed.

raw_input("Please make sure you are logged into Flickr in Firefox")


## The following opens the link in Firefox under a Linux installation
## There is a better way, but this way is fine for now.

firefox=os.popen( 'firefox \"' + link + '\"' )


## A firefox window will open asking the user if they wish to allow
## your program/api key to access their account. Once they have
## allowed it, the program can continue, so you need to ask
## the user if they have.

raw_input("A Firefox window should have opened. Press enter when you have authorized this program to access your account")


## A token is needed, which can now be generated with the
## following line of code.

token = myAuth.getToken(frob)


## This token cannot be got again, so the program will
## need to store it somewhere. The following lines
## of code will save it to token.txt in the current
## working directory.

f = file('token.txt','w')
f.write(token)
f.close()

```

_**Notice:** Flickr.py now adds a module to do this automatically. the module is `userToken()` (if calling from your program, `flickr.userToken()` will return the token), which can be called if you have it **setup correctly**. Examine the code for more information on setting it up correctly._

This token will need to be used every time the program is opened. To read token.txt, use the following lines of code. The user will need to be in the same working directory. You will need to alter your program in a way so that it is a definite location or something that doesn't require the user to be in the same working directory if not needed, though that isn't relevant to this tutorial.

```
f = file('token.txt','r')
token = f.read()
f.close()
```

_**Notice:** The next 2 sections are obsolete, as the `_doget()` method/function now automatically creates an api\_signature automatically. When calling `_doget()`, you will need to add `auth=True` to the call (example: `_doget(method, auth=True)`). The user must be already authenticated, as explained in the previous steps, and the `_doget()` function must be able to obtain token from the `userToken()` function (examine code for more information)._

**It is still benificial to your understanding of the Flickr Authentication system to still read it**

# ~~How To Call an API Method with Authentication~~ #

When calling an api method (such as flickr.photos.getSizes), you will need to add 2 other arguments to the call. The current arguments for flickr.photos.getSizes are `api_key`, `photo_id` and `method`, which is the actual name of the method (flickr.photos.getSizes). Lets take a look at the `getURL` function (which uses the api method flickr.photos.getSizes) within python.py to help explain this.

```
    def getURL(self, size='Medium', urlType='url'):
        """Retrieves a url for the photo.  (flickr.photos.getSizes)

        urlType - 'url' or 'source'
        'url' - flickr page of photo
        'source' - image file
        """
        method = 'flickr.photos.getSizes'
        data = _doget(method, photo_id=self.id)
        for psize in data.rsp.sizes.size:
            if psize.label == size:
                return getattr(psize, urlType)
        raise FlickrError, "No URL found"
```

The line I wish to focus on is `data = _doget(method, photo_id=self.id)`. The first argument to `_doget` is **always** the method, so all that has to be passed there is what method is being used (as stated in the method variable). This counts for 1 out of 3 arguments.

The rest of the arguments (no matter what number) to `_doget` require the api argument name before hand, then and equals sign, and then the value for the api argument. In this case, there is `photo_id` which is the name of the api argument, and `self.id` which is the value of the argument (that variable is defined outside the code snippet). This counts for 2 out of 3 arguments.

The third argument, api\_key, is automatically handled by `_doget`, as the API\_KEY variable is defined at the beginning of the page and is always required. To make it easier to learn, lets take a look at the actual URL it calls (it opens this URL, and reads the XML information):

**http://flickr.com/services/rest/?method=flickr.photos.getSizes&api_key=[your api key]&photo\_id=[photo id](your.md)**

You can try to load that up in your browser if you wish, you just need to replace [api key](your.md) with your api key (obviously), and [photo id](your.md) with the ID of a PUBLIC photo (that URL does not yet contain the arguments needed to authenticate yet).


## ~~API Signature~~ ##

The reason I explained the above was so you could understand how an API signature is made. I mentioned above that there are 2 other arguments to call to allow access to the account that is authenticated (for example, accessing photos set to private in that account). Those 2 arguments are `auth_token` and `api_sig`. `auth_token` is the token that was generated in a previous step (in the variable "token").

The argument `api_sig` is a signature that can be generated using every other argument. Basically, you generate one big string based on the following:
  * Your API Secret
  * The arguments being called, in alphabetical order, and then each argument value after the argument name.

The second point was a bit long, so lets use a practicle example. The arguments being called in `flickr.photos.getSizes` **in alphabetical order** are:
  * api\_key
  * auth\_token
  * method
  * photo\_id

Obviously, since we are generating the value of `api_sig`, we cannot include it in the list, and it doesn't need to be included (it can't be included, as you need the values of those arguments).

We generate a string that looks something like this:
`[API SECRET] + 'api_key' + [API_KEY] + 'auth_token' + [TOKEN] + 'method' + [METHOD] + 'photo_id' + [PHOTO ID]`

An example of this string from the flickr user authentication documentation:
`000005fab4534d05*api_key*9a0554259914a86fb9e7eb014e4e5d52*auth_token*45-76598454353455*method*flickr.photos.getSizes*photo_id*65641634653221`

That may look a bit confusing, so I have put in asterisks around each api argument name, though these must **not** be included in the md5 hash that is created later on.

Some Python code to generate that for you, assuming all the variables exist, is:

```
api_sig_plain = API_SECRET + 'api_key' + API_KEY + 'auth_token' + token + 'method' + method + 'photo_id' + self.id
```

With this, you will need to MD5 the string. For example, the MD5 hash of the example above is: 20592f7dab1e409081fc6c8121f06366

To generate the md5 code within Python, you will need to use the following code:

```
api_signature = hashlib.md5(api_sig_plain).hexdigest()
```

You will be able to then add this as the second additional argument to the api method that you are calling (the first additional argument being auth\_token). Lets see what the code that we saw earlier would look like if we added these additional modifications. This code is assuming all changes are made to flickr.py.

```
    def getURL(self, token, size='Medium', urlType='url'):
        """Retrieves a url for the photo.  (flickr.photos.getSizes)

        urlType - 'url' or 'source'
        'url' - flickr page of photo
        'source' - image file
        """
        method = 'flickr.photos.getSizes'
        api_sig_plain = API_SECRET + 'api_key' + API_KEY + 'auth_token' + token + 'method' + method + 'photo_id' + self.id
        api_signature = hashlib.md5(api_sig_plain).hexdigest()

        data = _doget(method, photo_id=self.id, auth_token=token, api_sig=api_signature)
        for psize in data.rsp.sizes.size:
            if psize.label == size:
                return getattr(psize, urlType)
        raise FlickrError, "No URL found"
```

A few notes on this. The token variable will have to be sent to the method getURL() from wherever it is being called from. For example, if it is being called from the example main.py file we created above, the call would look something like (remembering that a variable called token had already being created):

```
class = flickr.Photo()
class.getURL(token)
```

That explains where it gets the token variable from. The other variables are already within the document/already supplied somewhere else (the photo ID variable may need to be added to the call, though I am unsure of that).

The other line I wish to bring your attention to is `data = _doget(method, photo_id=self.id, auth_token=token, api_sig=api_signature)`. As you will notice, the 2 additional arguments were added to _doget in a familiar fashion. This should all be self explanatory (it basically adds those to the URL, in which an example was given above)._

Currently, for each individual method, a custom bit of code will need to be written to get the arguments in alphabetical order, and generate the api key, as that no code has been written for to do it automatically (though it is planned for the future). The code that we first added to flickr.py handles authentication to allow you to get the token to be used in every other api call, which is very important.

Just one note, if the authentication code is added to a function/method (such as we added it to getURL), and the user is running your program unauthenticated, you could run into some problems. It is a good idea to use a if statement to see if there is a token that can be used.

# Summing it all up #

If you need any help, please read the Flickr documentation that was linked to in reading material above, as it explains it better than I did, and it is a much shorter read. If you need any help at all, please do not hesitate to contact me, Joshua Henderson <joshhendo@gmail.com>. This can get very confusing, so I am happy to help.

![http://creativecommons.org/images/public/cc-GPL-a.png](http://creativecommons.org/images/public/cc-GPL-a.png)

[More Information](http://creativecommons.org/licenses/GPL/2.0/)