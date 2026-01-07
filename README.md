# router_frps
setting up frps on router

# Download ARM64 binaries
```
wget https://github.com/fatedier/frp/releases/download/v0.64.0/frp_0.64.0_linux_arm64.tar.gz
```
To unzip the files:
```
tar xzvf frp_0.64.0_linux_arm64.tar.gz
```
change the ini file to just the following line of code:
```
bindPort = 80
```

cp the frps executable and ini file to /usr/bin
```
cp frps /usr/bin
cp frps.ini /usr/bin
```
create the following text file in /etc/init.d directory:
The filename is also frps:
```
#!/bin/sh /etc/rc.common
START=98

start() {
        /usr/bin/frps -c /usr/bin/frps.ini
}

stop() {
        killall frps
        return 0
}
```

Change the file to executable by the following command:
```
chmod +x frps
```

# Enable and start frps

```
service enable frps
service start frps

```

# Open ports
Depending on your router, open the ports 80, 998 and 999

# Creating a dynamic domain name for router
Create an account in https://freedns.afraid.org/
Create your domain name for your router e.g. wjrouter.mooo.com

After that setup auto updating of IP address of your router through openwrt dynu auto update
