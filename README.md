# ufw ip forwarding (Reverse Proxy)

Vérifier l'état d'ufw :
```
ufw status
```
##

Activer ufw :
```
ufw enable
```
##

modifier /etc/default/ufw pour accepter les demandes de transfert :

```
nano /etc/default/ufw
```
- default_forward_policy = "accept"

##

éditer /etc/ufw/sysctl.conf pour autoriser le transfert ip :

```
nano /etc/ufw/sysctl.conf
```
- net.ipv4.ip_forward=1

##

modifier /etc/ufw/before.rules puis ajouter ce qui suit : 

```
nano /etc/ufw/before.rules
```

```
*nat
:PREROUTING ACCEPT [0:0]
:POSTROUTING ACCEPT [0:0]
-A PREROUTING -p tcp --dport 25565 -j DNAT --to-destination 80.80.80.80:25565
-A PREROUTING -p udp --dport 25565 -j DNAT --to-destination 80.80.80.80:25565
-A POSTROUTING -d 80.80.80.80 -j MASQUERADE

COMMIT
```
##

```
ufw reload
```
