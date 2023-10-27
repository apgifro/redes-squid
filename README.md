# Squid

Debian 11
Squid 4.13

## Arquivos editados

```
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
