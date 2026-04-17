# vps — Initialisation de serveur pour les étudiants

Scripts d'initialisation rapide pour un serveur Linux (Debian / Ubuntu) dans le cadre du cours.

### Avec un nom d'usager personnalisé

Par défaut, le script crée l'usager `nadine`. Pour utiliser votre propre prénom :

```bash
curl -fsSL https://raw.githubusercontent.com/nadine-professeur/vps/refs/heads/main/bin/initialiser-serveur.sh -o /tmp/init.sh && sudo bash /tmp/init.sh prenom-etudiant
```
## Initialisation rapide

Sur un serveur fraîchement installé, en tant que `root` (ou avec `sudo`), exécutez :

```bash
curl -fsSL https://raw.githubusercontent.com/nadine-professeur/vps/refs/heads/main/bin/initialiser-serveur.sh -o /tmp/init.sh && sudo bash /tmp/init.sh
```

### Version avec wget

```bash
wget -qO /tmp/init.sh https://raw.githubusercontent.com/nadine-professeur/vps/refs/heads/main/bin/initialiser-serveur.sh && sudo bash /tmp/init.sh
```

## Ce que fait le script

Le script `bin/initialiser-serveur.sh` exécute les étapes suivantes :

### 1. Création d'un usager sudo
- Crée un nouvel usager (interactif : demande le mot de passe)
- Ajoute l'usager au groupe `sudo`

### 2. Configuration du firewall UFW
- Installe `ufw`
- Remise à zéro des règles (`ufw reset`)
- Ouvre les ports :
  - `22/tcp` (SSH)
  - `80/tcp` (HTTP)
  - `443/tcp` (HTTPS)
- Active le firewall

### 3. Installation de l'éditeur jed
- Installe `jed` pour édition manuelle ultérieure

### 4. Installation et configuration de fail2ban
- Installe `fail2ban`
- Conserve une copie du fichier original : `/etc/fail2ban/jail.conf.copie`
- Crée `/etc/fail2ban/jail.local` avec la configuration `[sshd]` suivante :

```ini
[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 5
findtime = 300
bantime = 28800
```

Traduction : **5 tentatives ratées en 5 minutes → bannissement de 8 heures**.

- Redémarre et active le service `fail2ban` au démarrage

## Commandes utiles après l'installation

```bash
# Voir les règles du firewall
sudo ufw status verbose

# Voir l'état de fail2ban
sudo systemctl status fail2ban

# Voir les IP bannies sur SSH
sudo fail2ban-client status sshd

# Debannir une IP manuellement
sudo fail2ban-client set sshd unbanip 192.168.1.100
```
