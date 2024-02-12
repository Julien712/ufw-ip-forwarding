# Réinitialisation d'Iptables

La réinitialisation d'Iptables peut se faire en supprimant toutes les règles existantes et en remettant les politiques par défaut. Voici comment vous pouvez réinitialiser les règles d'Iptables :

**Supprimer toutes les règles et politiques existantes :**

```bash
sudo iptables -F
sudo iptables -X
sudo iptables -t nat -F
sudo iptables -t nat -X
sudo iptables -t mangle -F
sudo iptables -t mangle -X
sudo iptables -P INPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
sudo iptables -P OUTPUT ACCEPT
```

Ces commandes supprimeront toutes les règles existantes dans les tables filter, nat et mangle, ainsi que les chaînes personnalisées. Elles définissent également les politiques par défaut sur ACCEPT.

##

**Réinitialiser les compteurs de paquets et de bytes :**

```bash
sudo iptables -Z
```

Cette commande réinitialise les compteurs de paquets et de bytes des règles d'Iptables.

##

**Réinitialiser les règles persistantes (si vous utilisez un système qui les sauvegarde, comme iptables-save/iptables-restore) :**

```bash
sudo service iptables restart
```

Ou bien, si vous utilisez un système basé sur systemd :

```bash
sudo systemctl restart iptables
```

N'oubliez pas que la réinitialisation d'Iptables peut potentiellement interrompre la connectivité réseau, assurez-vous donc de comprendre les conséquences avant de procéder, surtout si vous travaillez à distance sur un serveur. Si vous avez un pare-feu actif, vous voudrez peut-être ajuster les règles en conséquence après la réinitialisation.
