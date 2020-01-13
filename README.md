# shadowsocks + privoxy/tsocks

- `shadowsocks`: service, socks proxy
- `privoxy`: service, convert socks proxy to http proxy
- `tsocks`: tool, use socks proxy for any command line

## Usage

### use `shadowsocks+privoxy` in host os
```
$ export http_proxy=http://127.0.0.1:8118
$ export https_proxy=https://127.0.0.1:8118
$ export no_proxy=localhost,127.0.0.0/8,::1,/var/run/docker.sock,mirrors.163.com,ruby.taobao.org,192.168.1.137
$ curl http://www.google.com
```

### use host os's proxy in docker container/Dockerfile
```
FROM ubuntu:14.04
ENV http_proxy http://172.17.0.1:8118
ENV https_proxy https://172.17.0.1:8118
ENV no_proxy localhost,127.0.0.0/8,::1,mirrors.163.com
ADD http://mirrors.163.com/.help/sources.list.trusty /etc/apt/sources.list
```

### use `shadowsocks+tsocks`
```
$ tsocks git clone https://github.com/hyperhq/hyper.git
$ tsocks wget https://storage.googleapis.com/golang/go1.5.3.linux-amd64.tar.gz
```

## Install and config shadowsocks

### install shadowsocks
```
$ sudo yum install python-pip
$ sudo pip install shadowsocks
```

### shadowsocks client config
```
$ cat /etc/shadowsocks/client.json 
{
    "server"  : "x.x.x.x",
    "server_port": 8388,
    "local_port": 1080,
    "password": "aaa123aa",
    "timeout": 600,
    "method": "aes-256-cfb"
}
```

### shadowsocks service config
```
$ sudo wget https://gist.githubusercontent.com/Jimmy-Xu/76a39aaee5e8515e06bd/raw/136905c597fdec070635129db60a88700b157733/sslocal -O /etc/init.d/sslocal
$ sudo chmod +x /etc/init.d/sslocal
```

### start shadowsocks service
```
$ sudo service sslocal start
```

### check shadowsocks service
```
$ service sslocal status
$ sudo netstat -tnopl | grep 1080
$ tail -f /var/log/sslocal.log
```

## Install and config privoxy

### install privoxy
```
$ sudo apt-get install privoxy
```

### config privoxy
```
$ cat /etc/privoxy/config
forward-socks5 / 127.0.0.1:1080 .
listen-address  0.0.0.0:8118
```
### start privoxy service
```
$ sudo service privoxy start
```

### check privoxy service
```
$ service privoxy status
$ sudo netstat -tnopl | grep 8118
```

## Install and config tsocks

### install tsocks
```
$ sudo apt-get install tsocks
```

### config tsocks
```
$ cat /etc/tsocks.conf
local = 192.168.1.0/255.255.255.0
server = 127.0.0.1
server_type = 5
server_port = 1080
``` 
