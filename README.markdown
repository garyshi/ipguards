# Setup iptables

You must create the chain and redirect traffic to it in default
iptables setup:

> `iptables -N ipguards_input`

> `iptables -A INPUT -p tcp --dport 22 -j ipguards_input`

For each accepted IP address, the daemon will do:

> `iptables -A ipguards_input -s IPADDR -j ACCEPT`

It's preferred to add some IP addresses before the redirect rule
to make the port accessiable in failures.

# The Daemon

The `ipguards` daemon maintains iptables by listens to a unix
socket for updates from the cgi program (writable by www-data),
and checks the db file to remove expired IP addresses.

When it finds the cgi module is working properly, it will add a
final rule to the `ipguards_input` chain to drop all (mismatch)
packages. Otherwise, it will make sure this rule doesn't exist
so everybody can connect, and hopefully send emails to notify the
problem.

