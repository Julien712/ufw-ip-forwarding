# Openvpn (Reverse Proxy)

**Mettre à jour le système**

```bash
sudo apt-get update && apt-get upgrade
```
## Installer openvpn sur le serveur

**Suivre ce tuto :**
https://www.youtube.com/watch?v=wnHslYZCiTE

**Lien vers le github du script d'installation openvpn:**
https://github.com/angristan/openvpn-install

**Surveiller à la fin du script ou est stocker le fichier de configuration pour le client**
- Pour ma part c'est : `/home/ubuntu/client.ovpn`

## Installer openvpn sur le client (Un conteneur LXC Proxmox) 

**Pour utilisé openvpn dans les conteneur il faut modifier le fichier de configuration pour ajouter le périphérique /dev/net/tun en suivant la doc :**

https://pve.proxmox.com/wiki/OpenVPN_in_LXC

**Installer openvpn :**

```bash
sudo -i
```

```bash
apt install openvpn
```

**Coller la configuration client généré sur le serveur dans :**
```bash
nano /etc/openvpn/client.conf
```

**Vérifier si le vpn démarre et que tout fonctionne :**
```bash
sudo openvpn /etc/openvpn/client.conf
```

**CTRL + C pour quitter**

**Activer openvpn au démarrage :**
```bash
 sudo systemctl enable openvpn@client.service
```

**Démarrer le service :**
```bash
 sudo systemctl start openvpn@client.service
```

**Vérifier que le client utilise bien l'ip du serveur vpn :**
```bash
 curl ifconfig.me
```

## Configurer ufw sur le serveur openvpn

**Vérifier l'état d'ufw :**
```bash
ufw status
```

**⚠️ IMPORTANT ! Autoriser le ssh pour éviter de bloquer la connexion à la VM :**
```bash
ufw allow ssh
```

**Activer ufw :**
```bash
ufw enable
```

**modifier /etc/default/ufw pour accepter les demandes de transfert :**

```bash
nano /etc/default/ufw
```
- default_forward_policy = "accept"


**éditer /etc/ufw/sysctl.conf pour autoriser le transfert ip :**

```bash
nano /etc/ufw/sysctl.conf
```
- net.ipv4.ip_forward=1


**modifier /etc/ufw/before.rules puis ajouter ce qui suit au début du fichier :**

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

**Recharger ufw pour appliquer les changements :**

```bash
ufw reload
```
