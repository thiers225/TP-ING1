# TP1: Environnement de travail et observation de HTTP



Architectures et applications web : Ingénieur 1ère année 

## Objectif et organisation

**Objectif :** disposer d'une pile web locale et d'un dépôt Git fonctionnels, puis observer concrètement le dialogue client-serveur. 
Les commandes sont données pour **Windows 10/11** (dans PowerShell ou Git Bash) et pour **Linux** (Ubuntu/Debian, dans un terminal).

**Prérequis :** un ordinateur sur lequel vous avez les droits d'administration et un compte GitHub. (s'assurer que tous les étudiants ont un compte Github à la fin de cette séance.)

## Partie A : Mettre en place l'environnement 

### A.1 Installer la pile web (Apache, PHP, MySQL/MariaDB)

*Windows :* télécharger XAMPP sur apachefriends.org, l'installer dans `C:\xampp`, ouvrir le panneau de contrôle XAMPP et cliquer sur **Start** pour Apache et MySQL. Le dossier web est `C:\xampp\htdocs`. Si Apache refuse de démarrer, le port 80 est souvent occupé (IIS, Skype) : fermer le logiciel en cause ou changer le port dans `httpd.conf`.

*Linux :* Installer les paquets natifs (pour ceux qui ont des systèmes Linux. Se baser également sur la documentation de la distribution)
```bash
sudo apt update
sudo apt install apache2 php libapache2-mod-php php-mysql mariadb-server
sudo systemctl enable --now apache2 mariadb   # démarrer et lancer au boot
systemctl status apache2 --no-pager            # vérifier : « active (running) »
php -v                                         # relever la version de PHP
```

Sur Windows, créer la base `app_ing2` (interclassement `utf8mb4_unicode_ci`) depuis phpMyAdmin : `http://localhost/phpmyadmin`. Sur Linux, phpMyAdmin s'installe en option avec `sudo apt install phpmyadmin`.

### A.2 Installer l'éditeur

Installer Visual Studio Code (code.visualstudio.com ; sous Linux `sudo snap install code --classic`), puis les extensions *PHP Intelephense* et *Live Server*.

### A.3 Installer et configurer Git

*Windows :* installer Git for Windows, depuis git-scm.com ou en ligne de commande :

```powershell
winget install --id Git.Git -e --source winget
```

Pendant l'installation, garder les choix par défaut sauf : éditeur par défaut *Visual Studio Code*, nom de la branche initiale *main*. Fermer puis rouvrir PowerShell avant de continuer.

*Linux :*

```bash
sudo apt update
sudo apt install git          # Fedora : sudo dnf install git
```

*Vérifier l'installation (les deux systèmes) :*

```bash
git --version
```

*Configurer son identité et ses préférences (les deux systèmes) :*

```bash
git config --global user.name "Prénom Nom"
git config --global user.email "prenom.nom@esatic.edu.ci"
```

Contrôler le résultat avec `git config --list`.

### A.4 Créer le projet et faire le premier commit

*Windows (PowerShell) :*

```powershell
cd C:\xampp\htdocs
mkdir apping
cd apping
git init
Créer un fichier info.php et y ajouter du  contenu 
<?php
phpinfo();
?>
```

*Linux :*

```bash
cd /var/www/html/apping
git init
Créer un fichier info.php et y ajouter du  contenu 
<?php
phpinfo();
?>
```

*Puis, sur les deux systèmes :*

```bash

git add .                     # les préparer pour le commit
git status                    #
git commit -m "TP1 : initialisation du projet"
```

Ouvrir `http://localhost/apping/info.php` et relever la version de PHP et les extensions chargées (`pdo_mysql` doit apparaître).

### A.5 Publier le dépôt sur GitHub

1. Créer un dépôt **vide** nommé `apping` sur GitHub 
2. Créer une clé SSH pour s'authentifier sans mot de passe. OpenSSH est inclus dans Windows 10/11 et dans Linux.

```bash
ssh-keygen -t ed25519 -C "prenom.nom@esatic.ci"   # valider l'emplacement par défaut, choisir une phrase de passe
```

Afficher la clé publique pour la copier :

```powershell
# Windows
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub | Set-Clipboard
```

```bash
# Linux
cat ~/.ssh/id_ed25519.pub
```

3. La coller dans *Settings › SSH and GPG keys* (GitHub) :

```bash
ssh -T git@github.com        
```

4. Relier le dépôt local au dépôt distant et l'envoyer (remplacer `<utilisateur>`) :

```bash
git remote add origin git@github.com:<utilisateur>/apping.git
git remote -v                  
git push -u origin main
```

## Partie B : Observer HTTP 
1. Ouvrir les outils de développement (F12), onglet **Réseau**, puis charger trois sites différents. Pour chacun, relever : nombre de requêtes, poids total, temps de chargement, version de HTTP.
2. Choisir une requête et noter sa méthode, son code de statut et cinq en-têtes de requête et de réponse ; expliquer le rôle de chacun.
3. Provoquer volontairement une erreur 404 (par exemple `http://localhost/apping/test.php`) et une redirection 301 ou 302 ; décrire ce qui se passe.
4. Interroger le DNS sur deux noms de domaine et relever les adresses IP obtenues :

```bash
nslookup www.polytechnique.edu     # Windows et Linux
dig www.polytechnique.edu           # Linux (paquet dnsutils si absent)
```

5. Envoyer une requête HTTP en ligne de commande et identifier la ligne de statut, les en-têtes et le corps :

```powershell
# Windows : écrire curl.exe, car « curl » seul désigne Invoke-WebRequest dans Windows PowerShell
curl.exe -i https://example.com
curl.exe -I http://localhost/apping/info.php   # en-têtes seuls (requête HEAD)
```

```bash
# Linux
curl -i https://example.com
curl -I http://localhost/apping/info.php
```

6. Rédiger le compte rendu dans un fichier `cr_tp.txt` à la racine du projet, puis le versionner :

```bash
git add cr_tp.txt
git commit -m "TP1 : compte rendu sur HTTP"
git push
```

## Questions et livrables

**Questions à traiter dans le compte rendu :**

1. Pourquoi dit-on que HTTP est sans état ?
2. Que contient la réponse à une requête `HEAD` ?
3. Pourquoi `info.php` ne doit-il jamais être déployé en production ?

**Livrables :**

- [ ] Lien du dépôt contenant les fichiers commités.
