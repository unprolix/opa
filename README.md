OPA: Onion Plugin for Apache
============================

OPA is an onion router supplied as an Apache module. It is separate
from, but may eventually interoperate with, the TOR network.

Traffic to and from OPA uses the PLATE protocol, which looks like
regular GET and POST web requests. It is impossible to prove from the
outside that anything special is going on besides regular web
processing. A web server may even configure OPA as an add-on that
functions in tandem with a vanilla page that is in use for other
purposes.

OPA is by default configured not to be very active. By leaving it in
its default configuration, a machine is not doing very much extra on
the network, in memory, or on disk.

A route only exists for a single end-user request. There are no
circuits that get re-used.

Node discovery works by a gossip protocol implemented by every
node. By default, nodes are configured to lie a certain proportion of
the time about other "known" OPA nodes.

This project is in a very early stage.
