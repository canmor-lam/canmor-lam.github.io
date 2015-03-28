---
layout: post
title:  Setup Shadowsocks Proxy for Terminal on OS X
date:   2015-03-29 03:18:51
categories: 
---
# Setup Shadowsocks Proxy for Terminal on OS X

Use *privoxy* to forward *http* proxy to *socks5* proxy.

1. Install *privoxy* by using *brew*:

    ```bash
    $ brew install privoxy
    ```

2. Add these lines to `/usr/local/etc/privoxy/config`:

    ```
    listen-address [::1]:8118
    listen-address  127.0.0.1:8118
    forward-socks5 / localhost:1080 .
    ```
    
    > ##### Note:
    > 
    > 1. There might be already a `listen-address 127.0.0.1:8118` line in the file, **do not** repeat it.
    > 2. `listen-address [::1]:8118` is for IPv6, it's **important** because it's requried for some tools (as I known: wget).

        
3. Add these scripts to your shell profile (for bash on OS X, it's `~/.bash_profile`) for helping enable/disable proxy:

	```bash
	function set_proxy_on()
	{
	    /usr/local/sbin/privoxy /usr/local/etc/privoxy/config
	    export http_proxy='http://localhost:8118'
	    export https_proxy=$http_proxy
	    export ftp_proxy=$http_proxy
	    export rsync_proxy=$http_proxy
	}

	function set_proxy_off()
	{
	    unset http_proxy
	    unset https_proxy
	    unset ftp_proxy
	    unset rsync_proxy
	    ps -A | grep sbin\/privoxy | grep -v grep | cut -d' ' -f2 | xargs kill -9
	}
	```

	Then reload profile:

	```bash
	$ source ~/.bash_profile
	```

4. Now, just run:

	```bash
	$ set_proxy_on
	```

	to turn on proxy. Run:
    
	```bash
	$ set_proxy_off
	```

	to turn off proxy. 
