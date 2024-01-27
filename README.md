# ufw ip forwarding (Reverse Proxy)

Mettre à jour le système.

```bash
sudo apt-get update && apt-get upgrade
```

Vérifier l'état d'ufw :
```
ufw status
```
##

⚠️ IMPORTANT ! Autoriser le ssh pour éviter de bloquer la connexion à la VM : 
```
ufw allow ssh
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

modifier /etc/ufw/before.rules puis ajouter ce qui suit au début du fichier : 

```
nano /etc/ufw/before.rules
```

```
*nat
:PREROUTING ACCEPT [0:0]
:POSTROUTING ACCEPT [0:0]

# Ajoutez cette règle pour rediriger le trafic UDP et TCP sur 1 seul port
# Remplacez 80.80.80.80 par l'ip du serveur sur lequel rédiriger le trafic
# et 25565 par le port à rediriger

-A PREROUTING -p tcp --dport 25565 -j DNAT --to-destination 80.80.80.80:25565
-A PREROUTING -p udp --dport 25565 -j DNAT --to-destination 80.80.80.80:25565
-A POSTROUTING -d 80.80.80.80 -j MASQUERADE

# Ajoutez cette règle pour rediriger le trafic UDP et TCP sur une plage de ports

-A PREROUTING -p tcp --dport 2000-49000 -j DNAT --to-destination 80.80.80.80:2000-49000
-A PREROUTING -p udp --dport 2000-49000 -j DNAT --to-destination 80.80.80.80:2000-49000
-A POSTROUTING -d 80.80.80.80 -j MASQUERADE

# DÉCONSEILLÉ Ajoutez cette règle pour rediriger TOUT le trafic UDP et TCP
# Noter que cette règle contraint tout le trafic vers 80.80.80.80
# et par conséquent empêche l'ouverture de certains ports sur la machine servant de proxy

-A PREROUTING -p tcp -j DNAT --to-destination 80.80.80.80
-A PREROUTING -p udp -j DNAT --to-destination 80.80.80.80
-A POSTROUTING -d 80.80.80.80 -j MASQUERADE


COMMIT
```
##

Recharger ufw pour appliquer les changements :

```
ufw reload
```
