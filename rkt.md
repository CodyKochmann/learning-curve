#### installing rkt

I was using coreos for most of my `rkt` testing since it comes preinstalled on the system. I have gotten this all to work on kali linux as well by follow the installing from a deb manually section of [the guide to installing rkt on other linux flavors](https://coreos.com/rkt/docs/latest/distributions.html).

#### for simple alpine rkt container

```bash
sudo rkt run --interactive --dns=8.8.8.8 quay.io/coreos/alpine-sh
``` 

#### for running rkt with a docker project (without needing docker installed I believe) 

```bash
rkt run --interactive --insecure-options=image --dns=8.8.8.8 docker://alpine
```

#### if networking is giving you trouble, this fixed it for me since that is the subnet of the default rkt pod network

```bash
iptables -A FORWARD -d 172.16.28.0/24 -j ACCEPT
iptables -A FORWARD -s 172.16.28.0/24 -j ACCEPT
```

#### for using rkt as a quick attach/detatch testing environment

This is a total hack... But, I was able to replicate `docker -d` with:

```bash
sudo nohup rkt run --interactive --dns=8.8.8.8 quay.io/coreos/alpine-sh --name=littlepig &
```

To get enter the container like `docker attach <name>` I needed to run `rkt enter <uuid>` so I made this to get that going
```bash
#!/bin/bash
# rkt-attach
# rkt's equivelent to "docker attach"

function uuid-of-name(){
  rkt list | awk "/$1/" | awk '{print($1)}'
}

function rkt-attach(){
  rkt enter $(uuid-of-name "$1") '/bin/sh'
}

rkt-attach "$1"
```

which then works fine with the following command

```bash
$ sudo ./rkt-attach littlepig
/ # 
/ # exit
```
