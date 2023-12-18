# MSPR_CICD
Code Java Hello World

## Séquence 1 : GitHUB
1° - Créer un compte sur GitHub : https://github.com/
Pour vous aider à créer votre compte GitHUB si besion : https://docs.github.com/fr/get-started/onboarding/getting-started-with-your-github-account

2° - Faire un fork du projet suivant : https://github.com/bstocker/MSPR_CICD.git
Support vidéo d'accompagnement pour "Forker" un projet : https://www.youtube.com/watch?v=yoBAszjRI3o

3° - Customiser le fichier index.html de votre repository comme suit :

<h1>Sample "Hello, World" Application</h1>
devient
<h1>Bonjour, ceci est notre application Groupe X</h1>

4° - Déclarer bstocker (avatar qui est un pingouin) comme collaborateur de votre repository.
Onglet Sittings -> Collaborateurs -> Manage access

Séquence 2 : Action GitHUB

Créer une Action dans GitHub permet de déployer un pakage fichier.war sur le serveur Web Tomcat.
Cette action passe par la création d'un fichier .yml dans GitHub qui sera déclanché à chaque commit des devellopeurs.

Travail demandé aujourd'hui : Pacakger votre dévellopement Java et le mettre en ligne.
Celle-ci sera ensuite accessible via l'adresse suivante : 
http://ec2-13-39-79-82.eu-west-3.compute.amazonaws.com:8080/MonAppli

!! Important : Remplacer "MonAppli" par votre NOM. Chacun d'entre vous aura alors sa propore application.

-------------
Etape 1: Création d'une action de packaging Java

Créer une action dans votre repository pour y déposer le script suivant :
name: Industrialisation continue sur le serveur AWS (Tomcat)
on: push
jobs:
  Deploy:
    name: Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - run: |
          jar cvf MonAppli.war * && ls
    

-----
Vidéo pour vous accompagner dans la création de vos actions :
[https://www.youtube.com/watch?v=3LsdtOJRVUk](https://www.youtube.com/watch?v=3LsdtOJRVUk)

-----

Travail demandé :
Créer votre action (script)
Faites des commits (en modifiant votre index.html par exemple)
Vérifier dans les logs de l'action (Deploy) si votre pakage a bien été créer (MonAppli.war)
Le fichier MonAppli.war doit être créé lors de la phase "RUN jar cvf groupeX.war * && ls"

## Séquence 2 : Mettre en ligne votre package sur un serveur Tomcat AWS (via une action de déploiement automatique)

Etape 2.1 : Modifier votre script d'action comme suit :

-------
name: Industrialisation continue sur le serveur AWS (Tomcat)
on: push
jobs:
  Deploy:
    name: Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - run: |
          jar cvf boris.war *
    - name: copy file via ssh password
      uses: appleboy/scp-action@master
      with:
        host: ${{ secrets.HOST_DNS }}
        username: ${{ secrets.USERNAME }}
        key: ${{ secrets.EC2_SSH_KEY }}
        port: ${{ secrets.DEPLOY_PORT }}
        source: "boris.war"
        target: "/opt/tomcat/webapps"

-----

Etape 2.2 : Vous avez besion de créer des secrets dans GitHUB

Vidéo pour créer un secret (ex : ${{ secrets.HOST_DNS }}) : https://www.youtube.com/watch?v=7CZde1a7rq0

Les informations dont vous avez besion :
HOST_DNS = ec2-13-38-7-106.eu-west-3.compute.amazonaws.com
USERNAME = admin
DEPLOY_PORT = 22
EC2_SSH_KEY = Le contenu de fichier (la clé) déposé dans Discord. TOUT LE CONTENU du fichier. De "-----BEGIN... à KEY-----"

## Sequence  3 : Qualité du code avec SonarQube
---------------------------------------------------------------------------
Vous avez à votre disposition un serveur SonarQube pour analyser la qualité de votre code.
Celui-ci est accessible via l'URL suivante :

http://ec2-15-188-65-116.eu-west-3.compute.amazonaws.com/

Login : admin
Password : rYGIfgiZ6uy=

Vous allez créer un projet pour votre repository GitHUB.

Create Projet -> Manually -> Projet Display name = Votre nom -> Set Up -> With GitHub Actions -> et suivre les instructions ...
A l'étape 2 (Create Workflow YAML File) : Choisir Other (for JS, TS, Go, Python, PHP, ...).

-----------------
Exercice 1 : Scanner votre projet Java "Hello World"
Corriger vos bugs de code.
Objectif : 0 bug !

## Séquence 4 : Utiliser le réseau IPFS pour sauvegarder ses données

---------------------------------------------------------------------------
[Installer IPFS sur le Noeud 1 du laboratoire]

//Se connecter au laboratoire numérique :
http://lab-boris.fr/
+ ADD NEW INSTANCE

----------------------
Astuce 1 : Passage en grand écran -> Alt+Enter

Astuce 2 : Coller du code dans le navigateur
 
Coller dans Chrome -> Ctrl+Shift+v
Coller dans Firefox ->  Shift+Insert
Coller dans EI et Safari -> Ctrl+v
-----------------------

//Installation du logiciel ipfs
apk add go-ipfs

//Initialisation
ipfs init
ipfs daemon &
[Enter]

//Création d'un fichier à sauvegarder
echo "Du texte dans un fichier" > fichierEPSI

ls

cat fichierEPSI

//Déposer un fichier sur le réseau IPFS
ipfs add fichierEPSI

//Conserver la clé afin de pouvoir restaurer votre fichier
QmRpYGXnkLXf5c1VVxHWvbktCjdDV1F6PGzkPcWpbqLmgL

//Restauration du fichier
ipfs get QmRpYGXnkLXf5c1VVxHWvbktCjdDV1F6PGzkPcWpbqLmgL -o restauration.txt


[Pour aller plus loin] - Pour voir les peers connectés à votre noeud
ipfs swarm peers

[Pour aller plus loin] - Manuel d'utilisation IPFS :
ipfs cat /ipfs/QmYwAPJzv5CZsnA625s3Xf2nemtYgPpHdWEz79ojWnPbdG/readme

--------------------------------------------------------
Exercice 1 : 
Créez 2 noeuds dans le laboratoire (+ADD NEW INSTANCE) et installer IPFS sur ce 2° noeud.
Réalisez un transfert de fichier entre ces 2 noeuds (entre les deux instances) via IPFS.

--------------------------------------------------------

Exerice 2 :
Dans GitHub, créer une action afin de sauvegarder sur le réseau IPFS votre package "fichier.war" 
---------------
name: Sauvegarde d'un fichier sur le réseau IPFS
on: push
jobs:
  Deploy:
    name: Deploy
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@master
      - run: |
          jar cvf fichier.war *
      - uses: jirutka/setup-alpine@v1
        with:
          branch: v3.15
      - run: |
          apk add go-ipfs
          ipfs init
          ipfs daemon &
        shell: alpine.sh --root {0}
      - name: Sauvegarde fichier war sur IPFS
        run: |
          ls -la 
          ipfs swarm peers
          ipfs add fichier.war
        shell: alpine.sh --root {0}


Remarque : Vous devez conserver votre job précédent (Lors de la séance Deploy AWS) puis ajouter à la suite le job de sauvegarde IPSF comme sur l'illustration suivante :
https://dispose.aphp.fr/u/5s2_eRxNv-P0zb95/3e50b938-756e-4eb9-b170-af5de4f23a5b?l


[OPTION : Pour les plus forts ]
Automatiser la diffusion de la clé de sauvegarde sur le canal Discord

Solution : ------------------
name: Industrialisation continue sur le serveur AWS (Tomcat)
on: push
jobs:
  build:
    name: Package AWS
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - run: |
          jar cvf boris.war *
    - name: copy file via ssh password
      uses: appleboy/scp-action@master
      with:
        host: ${{ secrets.HOST_DNS }}
        username: ${{ secrets.USERNAME }}
        key: ${{ secrets.EC2_SSH_KEY }}
        port: ${{ secrets.DEPLOY_PORT }}
        source: "boris.war"
        target: "/opt/tomcat/webapps"

  deploy:
    name: SAV IPFS
    runs-on: ubuntu-latest
    needs: build
    steps:
      - uses: actions/checkout@master
      - run: |
          jar cvf fichier.war *
      - uses: jirutka/setup-alpine@v1
        with:
          branch: v3.15
      - run: |
          apk add go-ipfs
          ipfs init
          ipfs daemon &
        shell: alpine.sh --root {0}
      - name: Sauvegarde fichier war sur IPFS
        run: |
          ls -la 
          ipfs swarm peers
          ipfs add fichier.war
        shell: alpine.sh --root {0}


[OPTION DISCORD]
Ajouter un serveur, créer le lien, pour mes amais est moi.
Allez dans salon que l'on a créé, paramètre, modififier le salon, à gauche intégration, WEBHOOTS, mettre un nom, récupérer l'URL et sauvegarder.
faire un secret avec l'URL.

    - name: Notification Discord
        env:
          DISCORD_WEBHOOK: ${{ secrets.DISCORD_WEBHOOK }}
        run: |
          ipfs_key=$(cat ipfs_key.txt)
          curl -X POST -H "Content-Type: application/json" -d "{\"content\": \"Le workflow IPFS est terminé. Clé IPFS : $ipfs_key\"}" $DISCORD_WEBHOOK

## Séquence 5 : Sauvegarder une base de données MySQL

---------------------------------------------------------------------------

Dans le laboratoire
//Installation du client MySQL
apk add mysql mysql-client

//Création d'un tunnel entre AWS et l'instance local
//Attention de ne pas oublier de déposer la clé dans l'instance (cliquez/déposer la clé dans la fenêtre noire de l'instance du laboratoire)
ssh -i "EPSI2023_G1.pem" admin@ec2-13-39-79-82.eu-west-3.compute.amazonaws.com -fN -L 0.0.0.0:3307:127.0.0.1:3306

//Sauvegarde en local de la base de données films (BDD sur AWS)
mysqldump -ucnam -P3307 -p"cnam" films > sauvegarde_films.sql

//Pour visualiser la sauvegarde de la base que vous venez de faire sur l'instance
ls


----------------------------------------------------
Les informations dont vous avez besion :
HOST_DNS = ec2-13-39-79-82.eu-west-3.compute.amazonaws.com
USERNAME = admin
DEPLOY_PORT = 22
EC2_SSH_KEY = Le contenu de la clé déposé dans Discord
MYSQLDUMP_PASSWORD = cnam

----------------------------------------------------------------------------
Exercice : Sauvegarder votre base de données MySQL sur IPFS depuis une action GitHUB

Solution :
name: Continuous Deployment and Database Backup

on: push

jobs:
  setup_and_backup:
    name: Setup SSH and Backup Database
    runs-on: ubuntu-latest
    steps:
      
      - uses: actions/checkout@v2
      
      
      - name: Setup SSH Keys
        run: |
          mkdir -p ~/.ssh
          echo "${{ secrets.EC2_SSH_KEY }}" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan ${{ secrets.HOST_DNS }} >> ~/.ssh/known_hosts
      
      
      - name: Create SSH Tunnel
        run: |
          ssh -i ~/.ssh/id_rsa -L 3307:127.0.0.1:3306 admin@${{ secrets.HOST_DNS }} -N &
          SSH_TUNNEL_PID=$!
          sleep 10
          if ps -p $SSH_TUNNEL_PID > /dev/null; then
            echo "SSH tunnel established, PID: $SSH_TUNNEL_PID"
          else
            echo "SSH tunnel failed"
            exit 1    
          fi
      
 
      - name: Dump MySQL Database
        env:
          MYSQL_PWD: ${{ secrets.MYSQLDUMP_PASSWORD }}
        run: |
          mysqldump -ucnam -h127.0.0.1 -P3307 --password="${MYSQL_PWD}" films > sauvegarde_films.sql
          if [ $? -ne 0 ]; then
            echo "Failed to dump MySQL"
            exit 1
          fi
      

      - name: Kill SSH Tunnel
        if: always()
        run: |
          kill $SSH_TUNNEL_PID || echo "SSH tunnel fin."
      

      - name: Install IPFS
        run: |
          wget https://dist.ipfs.io/go-ipfs/v0.9.1/go-ipfs_v0.9.1_linux-amd64.tar.gz
          tar xvfz go-ipfs_v0.9.1_linux-amd64.tar.gz
          cd go-ipfs
          sudo bash install.sh
          ipfs init
      
      
      - name: Add to IPFS
        run: |
          ipfs daemon --init &
          sleep 10
          ipfs add sauvegarde_films.sql
----------------------------------------------------
Les informations dont vous avez besion :
HOST_DNS = ec2-13-39-79-82.eu-west-3.compute.amazonaws.com
USERNAME = admin
DEPLOY_PORT = 22
EC2_SSH_KEY = Le contenu de la clé déposé dans Discord
MYSQLDUMP_PASSWORD = cnam
