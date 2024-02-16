# OpenVPN (Reverse Proxy)

Mettre à jour le système :

```bash
sudo apt-get update && apt-get upgrade
```

## Installer OpenVPN sur le serveur

Suivre ce tuto :
https://www.youtube.com/watch?v=wnHslYZCiTE

Lien vers le github du script d'installation OpenVPN :
https://github.com/angristan/openvpn-install

**Surveiller à la fin du script où est stocké le fichier de configuration pour le client**

- Pour ma part c'est : `/home/ubuntu/client.ovpn`

## Installer OpenVPN sur le client (Conteneur LXC Proxmox) 

Pour utiliser OpenVPN dans les conteneurs LXC il faut modifier le fichier de configuration pour ajouter le périphérique /dev/net/tun en suivant la doc :

https://pve.proxmox.com/wiki/OpenVPN_in_LXC

Installer OpenVPN :

```bash
sudo -i
```

```bash
apt install openvpn
```

Coller la configuration client généré sur le serveur dans :

```bash
nano /etc/openvpn/client.conf
```

Vérifier si le vpn démarre et que tout fonctionne :

```bash
sudo openvpn /etc/openvpn/client.conf
```

**CTRL + C pour quitter**

Activer OpenVPN au démarrage :

```bash
 sudo systemctl enable openvpn@client.service
```

Démarrer le service :

```bash
 sudo systemctl start openvpn@client.service
```

Vérifier que le client utilise bien l'ip du serveur OpenVPN :
```bash
 curl ifconfig.me
```

## Configurer ufw sur le serveur

Vérifier l'état d'ufw :

```bash
ufw status
```

⚠️ IMPORTANT ! Autoriser le ssh pour éviter de bloquer la connexion à la VM :

```bash
ufw allow ssh
```

Activer ufw :

```bash
ufw enable
```

modifier /etc/default/ufw pour accepter les demandes de transfert :

```bash
nano /etc/default/ufw
```

- default_forward_policy = "accept"

éditer /etc/ufw/sysctl.conf pour autoriser le transfert ip :

```bash
nano /etc/ufw/sysctl.conf
```

- net.ipv4.ip_forward=1

modifier /etc/ufw/before.rules puis ajouter ce qui suit au début du fichier :

```bash
nano /etc/ufw/before.rules
```

```bash
*nat
:PREROUTING ACCEPT [0:0]
:POSTROUTING ACCEPT [0:0]

# Remplacer ens3 par l'interface réseau du trafic du vps accessible via l'ip publique (Visible avec la commande : ifconfig)
# Remplacer 10.8.0.2 par l'ip local du client openvpn
# Remplacer 2000:49000 par le ou les ports désirés

-A PREROUTING -i ens3 -p tcp --dport 2000:49000 -j DNAT --to-destination 10.8.0.2
-A PREROUTING -i ens3 -p udp --dport 2000:49000 -j DNAT --to-destination 10.8.0.2

COMMIT
```

Recharger ufw pour appliquer les changements :

```bash
ufw reload
```

Si ça ne fonctionne pas :

```bash
reboot
```

## Conseils concernant la configuration sur Pterodactyl :

Dans "allocations" sur le conteneur LXC où est le client Openvpn il faut soit renseigné : 

`10.8.0.2` pour autoriser **UNIQUEMENT** le trafic entrant du serveur Openvpn 

ou 

`0.0.0.0` pour tout le trafic local sur le conteneur LXC (Box et serveur Openvpn)
