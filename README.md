munin_pgpool
============

A [Munin](http://munin-monitoring.org/)-plugin for [pgpool-II](http://www.pgpool.net/mediawiki/index.php/Main_Page) written by [Viktor Petersson](http://viktorpetersson.com).

The plugin plots three values:

* Wait for connection
* Idle connections
* `num_init_children`

# Configuration

In order to authenticate to pgpool, we need to provide the username for `pcp_pool_status` in plugins.conf and set [.pcppass](http://www.pgpool.net/docs/pgpool-II-3.5.4/doc/pgpool-en.html#password_file) for postgres user.
If `env.pcppath` is not specified, the plugin executes `/usr/bin/pcp_pool_path`.

    [pgpool_*]
    user   postgres
    env.host  MyDBServer
    env.port  9898
    env.user  pooladm
    env.pcppath  /path/to/pcp_pool_status
    env.pid_file /var/run/pgpool/pgpool.pid 

# Requirements

There are two requirements for this plugin to work. 

The first one is the Python library 'psutil'. 

This can be easily installed using: `pip install psutil`

... or if you prefer easy_install: `easy_install psutil`

The second requirement is `pcp_pool_status`, which is part of pgpool-II. This is used to access information about the pool. 
It should also be said that this plugin *must* run on the server running pgpool since we look for processes (see below). 

# How the values are derived

## `num_init_children`

This value is simply retreived from `pcp_pool_status`.

## Wait for connection

This one is a bit more tricky. What the plugin does is to simply look for processes named "wait for connection request" (with some filtering).

We then just count the number of occurrences and plot the value.

## Idle connections

This works just as the above plugin, but we look for the string "idle in transaction".
