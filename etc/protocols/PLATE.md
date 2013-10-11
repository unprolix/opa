PLATE
=====

PLATE is the protocol used between end users and OPA nodes, and
between OPA nodes themselves. It is of course an acronym for *Protocol
for Latent Anonymous Transmission of Envelopes*.

Right now this is just a sketch of the protocol; we'll expand it on an
ongoing basis.

This design owes much to the TOR project, in which many of these ideas
originated or at least were popularized. As far as I can tell, the
innovation here is a kind of useful steganography and deniability,
coupled with a relatively clear path to getting lots of installations
which are low-touch and low-risk for node administrators.


Outgoing Requests
=================

Any normal HTTP GET or POST request may be properly formatted as a
PLATE request without disturbing any other processing that a web
server is likely to perform on a request.

To format a PLATE request, a client adds a single field to either the
GET or POST request. The field name is
&ldquo;&#x1f4a9;&#x2603;&rdquo;.  [This value is a placeholder; also I
don't know yet if UTF8 field names are likely to cause problems.]

The value of the PLATE field is encrypted with the receiving server's
public key, which is available from the directory service. (Note that
the server's key is never available from the server itself. This
presents a little bootstrapping problem.) The encrypted value is
base64 encoded (and potentially escaped) if it has to be to fit in
with the underlying transmission mechanism.  (That is, if it's a GET
request then it must be encoded and escaped properly but if it's a
multipart form submission then binary might be fine.)

Once decrypted, the value of the PLATE field contains either:

* a tuple consisting of a URL for the next hop, along with the
  encrypted payload for the server at that next hop

or

* a tuple consisting of a URL, a request method (GET or POST), HTTP
  header fields (and values), a request body, and a public key. This
  is essentially enough inforation to create a GET or POST request to
  the world outside the PLATE network, and enrypt the response for the
  end user.

If the URL refers to a TOR hidden service, and the PLATE server is not
configured with knowledge of any TOR servers, then the request must be
passed on to another PLATE server. This does spread knowledge of the
user's original request around to other exit nodes, so might not be
desirable.

The response that a server gives to the underlying HTTP request does
not change when the request is "really" a PLATE request. Instead, the
request is placed on an internal queue and the request is either
passed on to another PLATE server, or the URL is accessed
directly. The webserver hosting the PLATE implementation should generate
a reasonable HTTP response in some fashion.



Responses
=========

The nature of the PLATE protocol forces separation between original
client requests and responses to those requests.

When a PLATE request has been passed to another PLATE server, and the
response is ready for retransmission, the response is passed as a HTTPS
request back to the originating server for that hop, using the URL
from the "Referer" field in the originating underlying request.

When the exit request has been made and the result retrieved, the
result must be made available to the end user. A new onion route is
constructed for the trip back to the node on which the result will be
stored for the user. This route could be a reverse of the old route, a
new route from the same components, or a new route from a
larger/intersecting selection of nodes. The final destination of the
return route must either be the original entry node for the original
user request, or the original request must have contained a
specification for the return message.

The end user must poll the node to which its original request was sent
in order to retrieve the "real" response to its request. (Or, if as
above a different destination server was specified, then that node
needs to be polled.) The polling is somewhat distasteful but seems to be
required in order to prevent easy detection that something else is going
on here besides plain HTTPS.


Implementation Notes
====================

Ideally an implementation will minimize the amount of extra work that
can be remotely observed to take place on a web server correlated with
it receiving a PLATE request. It wouldn't do to have the entire
machine slow down noticeably. There are a few ways of addressing this,
and it doesn't necessarily need to be in early versions, but
implementors should be aware of the issue.

Implementors are encouraged to eliminate or minimize any logging of
data associated with PLATE requests in published software, to reduce
the chances of accidental configuration mistakes by machine administrators.
