# pymessagefocus2
This API client is intended to conform entirely to the [API documentation](https://app.adestra.com/doc/page/current/index/api) for Adestra MessageFocus and serves as a very thin layer in front of `xmlrpclib` which primarily transforms some non-conforming errors from the API to appear and behave as specified in the documentation.

There are three known reasons an exception might be raised:
 1. Adestra MessageFocus returned an error in the form of `xmlrpclib.Fault` (standard) or in the case of failed authentication `xmlrpclib.ProtocolError`. These are standardised under `pymessagefocus2.Fault` with the interface specified in the [error documentation](https://app.adestra.com/doc/page/current/index/api/event).
 2. Networking failure: `socket.error` or `httplib.BadStatusLine` will be raised if a retry fails inside of `xmlrpclib` ([source](https://hg.python.org/cpython/file/651f7addf4a8/Lib/xmlrpclib.py#l1284)).
 3. XML marshalling errors in the form of a `TypeError` ([source](https://hg.python.org/cpython/file/651f7addf4a8/Lib/xmlrpclib.py#l652)) may be raised by `xmlrpclib` if the values passed are not of a [conformable type](https://docs.python.org/2/library/xmlrpclib.html#xmlrpclib.ServerProxy)

## Marshalling
The Adestra MessageFocus API uses standard XML-RPC data structures, as such any reference in the documentation to a `Struct` refers to the `dict` type in python, and a `StructArray` simply refers to a list of dictionaries. For ease of use dates have been set to unmarshal as `datetime.datetime` rather than `xmlrpclib.DateTime` More information is availble on [this page](https://docs.python.org/2/library/xmlrpclib.html#xmlrpclib.ServerProxy).

## Usage
All API methods are accessible exactly as described in the API documentation once a `Client` instance has been created.
The following code makes a call to the [event search]() method of the API. Any exceptions caught by `except Fault as f` can be looked up [here](https://app.adestra.com/doc/page/current/index/api/error-handling)
```python
from pymessagefocus2 import Client, Fault

c = Client("organisation", "username", "password")
try:
    c.event.search({"type": "click"})
except Fault as f:
    print f.faultCode
    print f.faultString
```
