# Réinitialisé ufw

## Ces commandes servent à remettre UFW dans un état initial, supprimant toutes les règles personnalisées et les configurations que vous avez pu ajouter.

**D'abord, désactivez UFW avec la commande suivante :**

```bash
sudo ufw disable
```

##

**Ensuite, réinitialisez les règles par défaut avec :**

```bash
sudo ufw reset
```

##

⚠️**IMPORTANT ! Ne pas oublié de réautoriser le ssh pour éviter de bloquer la connexion à la VM : **

```bash
ufw allow ssh
```

##
**Vous pouvez maintenant réactiver UFW si nécessaire :**

```bash
sudo ufw enable
```
