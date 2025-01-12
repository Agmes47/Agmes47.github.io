+++
title = 'TCP_in_Rust'
date = 2024-10-03T14:31:59+08:00
draft = true
+++

[setcap](https://www.man7.org/linux/man-pages/man8/setcap.8.html)
[capabilities](https://www.man7.org/linux/man-pages/man7/capabilities.7.html)
I wonder how does Linux achieve this though.

```rust
    eprintln!("{:x?}",&buf[..3]);
```

[Explain about the {:?}](https://stackoverflow.com/a/38157410)


```fish
    sudo ip addr add 192.168.0.1/24 dev tun0     
    sudo ip link set up dev tun0
```

I want to know what are these two commands doing.  
Here are some examples on the man page:

    ip addr  
    # Shows addresses assigned to all network interfaces.

    ip neigh  
    # Shows the current neighbour table in kernel.

    ip link set x up  
    # Bring up interface x.

    ip link set x down  
    # Bring down interface x.

    ip route  
    # Show table routes.

I have met a `$!` inside a shell script, here are the explanation from claude:

> The $! is a special variable in shell scripting that contains the process ID (PID) of the last background command executed.

I got a new learn command, `trap`.

    trap - perform an action when the shell receives a signal  
    Usage:  
    trap [OPTIONS] [[ARG] REASON ... ]


[time: 44:40](https://www.youtube.com/watch?v=bzja9fQWzdA&t=16217s)
One thing that confused me is that why is the packet on the link layer(layer 2nd) has 4 bytes more than the packet on the IP layer(layer 3). I thought the size of the packet is just going to be more and more large as the layer number increases.
Wait, if we think about the process of the encapsulation and decapsulation, it actually makes sence. Maybe I am wrong for my previous thought.

It seems like the IPv4 encapsulation doesn't add any payload, um?

What is `!#` and `#` though?  And what about trait `Eq` and trait `PartialEq`?











