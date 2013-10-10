OPA: Onion Plugin for Apache
============================

OPA is an onion router supplied as an Apache module. It is separate
from, but may eventually interoperate with, the TOR network.

Traffic to and from OPA looks like regular GET and POST web requests
and it is impossible to prove from the outside that it is doing
anything else. A web server may even configure OPA as an add-on that
functions in tandem with a vanilla POST form that is in use for other
purposes.

It is by default configured not to be very active. By leaving it in
its default configuration, a machine is not doing very much extra on
the network, in memory, or on disk.

A route only exists for a single end-user request. There are no
circuits that get re-used.  A node (i.e. an apache server configured
with OPA) is configurable to be an exit at an arbitrary level of
probability for any request on a one-by-one basis.

Node discovery works by a gossip protocol implemented by every
node. By default, nodes are configured to lie a certain proportion of
the time about other "known" OPA nodes.

