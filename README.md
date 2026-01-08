# router_frps
setting up frps on router

# Install openwrt
+ Follow openwrt official site to install openwrt in the router
+ SSH should be enabled by default and you have to ssh using root username
+ Change the root password for security reason

# Change router port 80
The web interface to router is using port 80. We need to change port 80 to port 8080
```
vi /etc/config/uhttpd
```
change
```
list listen_http 0.0.0.0:80
```
to
```
list listen_http 0.0.0.0:8080
```
reboot router and check if the website is working:
```
http://192.168.0.1:8080
```

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
Before that, make sure the router web interface is change to other port such as port 8080

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
In openwrt, open the ports 80, 998 and 999.
Make sure WAN side can access to the port (this router device). Common mistake is to allow LAN side to access the port only
This is detailed instruction to open the ports:
+ under Network->Firewall
+ Click Add
+ Give the port a name e.g. frps for port 80
+ Source Zone: WAN
+ Destination Zone: Device (Input)
+ Destination port: 80, 998 or 999
+ Leave the rest as default
+ Save and Apply

# Creating a domain name for router
Create an account in https://freedns.afraid.org/
Create your domain name for your router e.g. wjrouter.mooo.com
Check your public ip address of your router (e.g.  112.199.254.252) and manually update the IP address of e.g. wjrouter.mooo.com

# Dynamic DNS update
If everything is fine with FRPS, last step is to install Dynamic DNS software in openwrt to dynamic update the domain IP.
Home router IP address may change after sometime. You can install luci-app-ddns package in openwrt to automatically update IP address in freedns when router public IP changes.
To access to dynamic DNS:
+ Services-> Dynamic DNS
+ In basic settings:
+ Lookup Hostname: rpoly.mooo.com
+ IP address version: IPv4 Address
+ DDNS Service provider: custom
+ Custom update-URL: https://freedns.afraid.org/dynamic/update.php?xxxxxxxxxxx
+ Domain: sync.afraid.org
+ Username: guess
+ password:xxxx

Note that you need to get the correct xxxx info for Custom update-URL and password from https://freedns.afraid.org/

# Points to note
Some important points:
+ After changing the domain IP address, it takes some time to propagate to the internet. Always ping the domain name to check if the ip is correct e.g. ping wjrouter.mooo.com
+ After the domain name IP has been verified, REBOOT the frpc machine i.e. the server

