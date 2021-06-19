---
title: Node.js http server host
datetime: 2020-06-04T20:49:16Z
tags: nodejs server
---
<time>10:08pm</time> My node server is publicly accessible using the IP, I thought by default node were only listening for connection coming from the same machine.
<!--more-->

<time>10:16pm</time> According to Node.js documentation when the server is not listening to any specific host, it will accept connection from `::` if IPv6 is available otherwise `0.0.0.0`.

> If host is omitted, the server will accept connections on the unspecified IPv6 address (::) when IPv6 is available, or the unspecified IPv4 address (0.0.0.0) otherwise.
>
> Source: [Node.js Net](https://nodejs.org/api/net.html#net_server_listen_port_host_backlog_callback)

<time>10:33pm</time> Another option for this is to reject connection to that port from outside using a firewall, such as IPTables.

<time>10:44pm</time> The changes are available on [github](https://github.com/wellingguzman/wellingguzman.com/commit/83dcec8633b494ed12b2f24e971e4621c3c431f5) that makes the server only accept connection from localhost.

<time>10:47pm</time> All changes pushed, the site is not longer publicly available through the IP.
