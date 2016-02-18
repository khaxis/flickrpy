For individual API functions/methods that I have implemented in Flickr.py, I will add documentation to an individual wiki page explaining how to use a number of them, as some may be slightly complicated to allow for maximum flexibility (not overly complicated, but may need a paragraph of explaining to understand how to use).

## TODO List ##

Last updated Dec 19, 2007

  * ~~flickr.blogs.getList~~
  * flickr.blogs.postPhoto
  * flickr.contacts.getList
  * flickr.groups.browse
  * flickr.groups.getActiveList
  * flickr.people.getOnlineList
  * flickr.photos.getContactsPhotos
  * flickr.photos.getContactsPublicPhotos
  * flickr.photos.getContext
  * flickr.photos.getCounts
  * flickr.photos.getExif
  * flickr.photos.getNotInSet
  * flickr.photos.getPerms
  * flickr.photos.getRecent
  * flickr.photos.getUntagged
  * flickr.photos.setDates
  * flickr.photos.setPerms
  * flickr.photos.licenses
  * flickr.photos.notes
  * flickr.photos.transform
  * flickr.photosets.getContext
  * flickr.photosets.orderSets
  * flickr.reflection (not important)
  * flickr.tags.getListPhoto
  * ~~flickr.urls~~ (partial, in progress)

## Flickr Upload ##

An additional file, flickrUpload.py, is planned to be released as apart of flickr.py, which will allow programs to not only read and edit accounts, but upload to accounts if required.

Currently, there is no release date planned.

## Automated Authentication ##

With the new Flickr Authentication system, you need to generate a API Signature manually. An api signature is a combination of your secret key, all the methods and their values put together and hashed with a bit of md5. For more information, refer to the authentication documentation.

Currently, because when I made the Authentication module, I didn't add automated support for this, you need to do it manually for each function you wish to call, which can take up a lot of time. Below is some code that I wrote to do this automatically. This would replace what is currently in the `if auth:` if statement in the function `_doget`. This is just copied and pasted from my "throw away" python file, which is where I test code. This could easily be implemented into the flickr.py script.

```
def _doget(method, auth=False, **params):  
    if auth:
        # Script has been edited to handle the new
        # Flickr API Authentication System
        url = ''
        paramaters = ['API_KEY', 'method']

        for item in params.items():
            paramaters.append(item[0])

        paramaters.sort()
        print paramaters

        api_string = API_SECRET
        for item in paramaters:
            for chocolate in params.items():
                if item == chocolate[0]:
                    api_string = api_string + item + chocolate[1]
            if item == 'method':
                api_string = api_string + 'method' + method
            if item == 'API_KEY':
                api_string = api_string + 'api_key' + API_KEY
                    
        print api_string

method = 'flickr.test.method'
API_KEY = 'myAPIKey'
API_SECRET = 'itsAsecret!!!'

_doget(method, auth=True, a='453534', c='34243', b='465254')
```

## flickr.blogs ##

I am currently working on adding blogging API functionality to flickr.py (`flickr.blogs.*`). Retriving the list of blogs is near completion.

Due to the reliance on authentication in these flickr API functions/modules, I am making improvements to the authentication system, which will make much of the authentication documentation currently written obsolete. For example: Currently, you need to manually make an API Signature, and manually sort all the parameters into alphabetical order. This will be done automatically when the new improvements (near completion) are implemented.


## List as of 2005/06/10 ##

In brackets is additional information about each item

^ = public only without authentication

```
TOOD list:
* flickr.blogs.*
* flickr.contacts.getList [requires authentication]
* flickr.groups.browse ^
* flickr.groups.getActiveList ^
* flickr.people.getOnlineList ^
* flickr.photos.getContactsPhotos [requires authentication]
* flickr.photos.getContactsPublicPhotos
* flickr.photos.getContext ^
* flickr.photos.getCounts ^
* flickr.photos.getExif ^
* flickr.photos.getNotInSet ^
* flickr.photos.getPerms ^
* flickr.photos.getRecent ^
* flickr.photos.getUntagged ^
* flickr.photos.setDates ^
* flickr.photos.setPerms ^
* flickr.photos.licenses.* ^
* flickr.photos.notes.* ^
* flickr.photos.transform.* ^
* flickr.photosets.getContext ^
* flickr.photosets.orderSets ^
* flickr.reflection.* (not important) ^
* flickr.tags.getListPhoto ^
* flickr.urls.* ^
```