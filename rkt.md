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
