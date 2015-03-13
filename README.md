RBLDNSD
=======

`rbldnsd` is a simple DNS server that allows to serve realtime blacklists (or whitelists) via DNS. All you need is a DNS zone file as described in the [rbldnsd documentation](http://www.corpit.ru/mjt/rbldnsd.html).
Then run the container with the `zonename:type:fname,fname,fname...` as the command and let the NS entry of your DNS zone point to the docker host.

Example usage via docker
------------------------

    docker run -it --rm \
        -v /path_to_your_zone_directory/:/var/lib/rbldnsd/:ro \
        -p 53:53/udp \
        kurthuwig/rbldnsd:latest \
        my-blacklist.example.com:ip4set:my-blacklist

Example usage via fig
---------------------

This is an example [fig](http://www.fig.sh/index.html) file to launch `rbldnsd`. You only have to change the `command` and maybe the source directory for the volume containing the zone files.

    rbldnsd:
      image: kurthuwig/rbldnsd:latest
      volumes:
       - /path_to_your_zone_directory/:/var/lib/rbldnsd/:ro
      ports:
       - "53:53/udp"
      command: "my-blacklist.example.com:ip4set:my-blacklist"

Testing
-------

Normal DNS blacklists list an entry for `127.0.0.2` for testing purposes. If you did this as well, you can test your setup on a unix system with the `host` command:

    $ host 2.0.0.127.my-blacklist.example.com. localhost
    Using domain server:
    Name: localhost
    Address: 127.0.0.1#53
    Aliases: 
    
    2.0.0.127.my-blacklist.example.com has address 127.0.0.2

and the negative test with

    $ host 1.0.0.127.my-blacklist.example.com. localhost
        Using domain server:
    Name: localhost
    Address: 127.0.0.1#53
    Aliases: 
    
    Host 1.0.0.127.my-blacklist.example.com. not found: 3(NXDOMAIN)
