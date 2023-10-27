# Squid

[Baixe o OVA no Google Drive](https://drive.google.com/drive/folders/167SecQqHaj53QvY4qL9IdG087qeHB-2f?usp=share_link)

Debian 11

Squid 4.13

![](/ssh.png)
![](/squid.png)

## Arquivos editados

```
nat.sh
/etc/squid/squid.conf                      
/etc/squid/squid.conf.deny.all.and.alllow.ifro 
/etc/squid/squid.conf.original                 
/etc/squid/ssl/squid.pem
```

## Comandos utilizados
```
./nat.sh
echo 1 > /proc/sys/net/ipv4/ip_forward
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
cat /etc/resolv.conf
apt install -y squid-openssl
squid --version
openssl req -new -newkey rsa:2048 -sha256 -days 365 -nodes -x509 -extensions v3_ca -keyout myCA.pem -out myCA.pem
mv myCA.pem squid.pem
/usr/lib/squid/security_file_certgen -c -s /var/lib/ssl_db -M 4MB
/usr/lib/squid/security_file_certgen -c -s /var/spool/squid/ssl_db -M 4MB
chown -R proxy:proxy ssl/                                                
chmod 700 /etc/squid/ssl/                                                
chown -R proxy:proxy /var/spool/squid/ssl_db/                            
squid -k reconfigure                                                     
systemctl restart squid                                                  
systemctl status squid                                                   
journalctl -xe
```

## `nat.sh`

```
echo "1" > /proc/sys/net/ipv4/ip_forward
iptables -t nat -A PREROUTING -p tcp --dport 443 -j REDIRECT --to-port 8443              
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
```

## `squid.conf` que bloqueia todos os sites e libera `ifro.edu.br`

```
http_port 3128

https_port 8443 intercept ssl-bump cert=/etc/squid/ssl/squid.pem generate-host-certificates=on
ssl_bump bump all

http_port 8080 intercept
acl FORWARD_MODE_LOCALPORT localport 3128

acl CONNECT method CONNECT

acl SSL_PORT port 443
acl SAFE_PORTS port 80
acl SAFE_PORTS port 443

acl SAFE_URLS dstdomain .ifro.edu.br

http_access deny !SAFE_PORTS
http_access deny CONNECT !SSL_PORT
http_access deny FORWARD_MODE_LOCALPORT

http_access allow CONNECT SSL_PORT
http_access allow SAFE_URLS

http_access deny all
```

## `squid.conf` que libera todos os sites e bloqueia `facebook.com`

```
http_port 3128                                                                                 
                                                                                               
https_port 8443 intercept ssl-bump cert=/etc/squid/ssl/squid.pem generate-host-certificates=on 
ssl_bump bump all                                                                              
                                                                                               
http_port 8080 intercept                                                                       
acl FORWARD_MODE_LOCALPORT localport 3128                                                      
                                                                                               
acl CONNECT method CONNECT                                                                     
                                                                                               
acl SSL_PORT port 443                                                                          
acl SAFE_PORTS port 80                                                                         
acl SAFE_PORTS port 443                                                                        
                                                                                               
acl DENY_URLS dstdomain .facebook.com                                                          
                                                                                               
http_access deny !SAFE_PORTS                                                                   
http_access deny CONNECT !SSL_PORT                                                             
http_access deny FORWARD_MODE_LOCALPORT                                                        
                                                                                               
http_access allow CONNECT SSL_PORT                                                             
http_access deny DENY_URLS                                                                     
                                                                                               
http_access allow all
```
