# Gitlab-learning

Exercices pratiques et notes personnelles pour apprendre à utiliser GitLab avec Docker sur Windows.

## documentation 

https://www.youtube.com/playlist?list=PLn6POgpklwWrRoZZXv0xf71mvT4E0QDOF
https://about.gitlab.com/fr-fr/

## installation sous docker 

https://docs.gitlab.com/install/docker/installation/

### Prérequis

1. Windows 10/11 Pro ou Entreprise
Hyper-V ou WSL2 doit être activé.

2. Docker Desktop

Télécharge et installe Docker Desktop depuis : https://www.docker.com/products/docker-desktop/
Active l’intégration WSL2 si disponible.

3. Créer le fichier docker compose 

https://github.com/Dadd76/gitlab-learning/blob/main/docker-compose.yml

```
version: '3.6'

services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    container_name: gitlab
    restart: always
    hostname: 'localhost'
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://localhost'
    ports:
      - '80:80'
      - '443:443'
      - '22:22'
    volumes:
      - ./config:/etc/gitlab
      - ./logs:/var/log/gitlab
      - ./data:/var/opt/gitlab
```

pour modifier l'url de gitLab : 

```
gitlab:
  environment:
    GITLAB_OMNIBUS_CONFIG: |
      external_url 'http://gitlab.local'
```

dans le fichier host ajouter en administrateur 

C:\Windows\System32\drivers\etc\host

`127.0.0.1 gitlab.local`

4. Lancer l'image 

```
docker-compose up -d
```

5. Récupération du mot de passe pour la première connection

`sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password`

6. accéder a gitLab

`http://gitlab.local`

Si ça ne fonctionne pas : vide le cache DNS avec ipconfig /flushdns dans PowerShell.
Assure-toi que ton pare-feu ne bloque pas les ports utilisés (80, 443).

# Migration  GitHub vers GitLab

migrer ton code de GitHub vers GitLab, tout en gardant ton historique Git (commits, branches, tags, etc.).

Tu as deux principales options :

✅ Option 1 – Import automatique via GitLab
Utilisation de l’outil d’import GitHub intégré dans GitLab.

✅ Option 2 – Migration manuelle avec Git (recommandée pour la maîtrise)
Cloner depuis GitHub, puis pousser sur GitLab.

### Option 1 : Import automatique via GitLab (GUI)
Connecte-toi à ton GitLab.

Va dans "New project" > "Import project" > "GitHub".

Connecte ton compte GitHub (OAuth).

Sélectionne le ou les dépôts à importer.

GitLab clonerait automatiquement les projets, avec branches, commits, issues (optionnel).

✅ Avantages :

Rapide

Peut inclure issues, labels, MRs, etc.

❌ Inconvénients :

Nécessite autorisation OAuth

Moins de contrôle

Ne fonctionne pas toujours avec GitLab auto-hébergé

### Option 2 : Migration manuelle avec Git (recommandée)

1. 📥 Cloner ton dépôt GitHub

```
git clone --mirror git@github.com:ton-user/ton-repo.git
cd ton-repo.git
```
--mirror clone toutes les branches, tags, et refs.

2. 🛠️ Créer un projet vide sur GitLab
Sur ton GitLab (self-hosted ou GitLab.com), crée un nouveau projet vide (sans README, licence, etc.)

Copie l’URL SSH du projet GitLab, par exemple :

git@gitlab.com:ton-user/ton-repo.git

3. Changer le remote pour pointer vers GitLab

`git remote set-url origin git@gitlab.com:ton-user/ton-repo.git`

4. Pousser tous les objets sur GitLab

`git push --mirror`

Cela envoie toutes les branches, tags et commits vers GitLab.

5. Après la migration
Clone ton repo depuis GitLab comme d’habitude :

`git clone git@gitlab.com:ton-user/ton-repo.git` 
Mets à jour les URLs de remote dans ton environnement local si besoin :

`git remote set-url origin git@gitlab.com:ton-user/ton-repo.git`

## Utilisation gitLab

### Créer un Personal Access Token sur GitLab (édition Free/CE incluse) :

Dans Gitlab Clique sur ta photo de profil en haut à droite → "Edit profile" ou "Préférences".

Va dans le menu de gauche : "Access Tokens" ou "Tokens d'accès personnels".

Remplis le formulaire :

Nom : Runner Registration (ou autre)
Expiration date : optionnel (tu peux le laisser vide ou définir une date)
Scopes (Permissions) :
  api (obligatoire)

(ne coche pas d'autres scopes inutiles si c’est juste pour le runner)

Clique sur "Create personal access token".
Copie le token immédiatement et garde-le bien en sécurité.
Tu ne pourras plus le voir ensuite.

### Étapes pour forcer l’utilisation des Merge Requests

1. 🔐 Protéger les branches critiques
Dans ton projet GitLab :

Va dans Project > Repository > Branches

Clique sur "Protect" à droite de la branche que tu veux verrouiller (ex: main)

Paramètre comme suit :

"Allowed to push" : No one (ou seulement Maintainers)

"Allowed to merge" : Maintainers ou une autre équipe responsable

Active "Require approval" si nécessaire

💡 Cela empêche tout push direct sur main : les contributeurs devront obligatoirement créer une Merge Request.

2. 🧷 Configurer des règles de merge strictes
Dans Settings > General > Merge requests, active les options suivantes pour renforcer le contrôle :

✅ "Only allow merge requests to be merged if the pipeline succeeds"

✅ "Prevent pushing to branches"

✅ "Require approval" et définis un nombre d'approbateurs (si besoin)

✅ "Allow only fast-forward merge" (optionnel pour clean history)

3. ⚠️ (Optionnel mais recommandé) Bloquer la création de branches sans MR
Dans GitLab Premium, tu peux aussi :

Restreindre la création de branches spécifiques uniquement aux mainteneurs

Imposer des politiques de commit signés, ou des règles de sécurité avancées

📌 Résultat :
Avec cette configuration :

Les développeurs ne peuvent plus pousser directement sur main

Toute contribution doit passer par une Merge Request

Tu peux centraliser le code review, la QA, les tests et la validation

🧠 Bonnes pratiques associées :
Utilise un template de MR (.gitlab/merge_request_templates) pour structurer les revues

Active les règles de CODEOWNERS pour désigner automatiquement les relecteurs

# Créer un pipeline GitLab CI/CD pour une application C# console

à la racine du projet : .gitlab-ci.yml

## Création gitlab-runner

`docker run -d --name gitlab-runner --restart always -v /srv/gitlab-runner/config:/etc/gitlab-runner -v /var/run/docker.sock:/var/run/docker.sock gitlab/gitlab-runner:latest`

correspond à l’identifiant unique (container ID) du conteneur Docker que tu viens de lancer avec la commande docker run. 
`0e83db854e2f1fba94137d4f1d2e5e2eac97d26a778d1ea541f607029dc0f2d0`

  1. L’image gitlab/gitlab-runner:latest est téléchargée
Elle est stockée localement dans Docker Desktop.

Tu peux la voir avec : `docker images`  

  2. Le conteneur gitlab-runner est créé
Le conteneur tourne dans l’environnement Docker Desktop (dans la VM Docker interne).

Tu peux le voir avec : `docker ps`
ou via l’interface graphique de Docker Desktop.

  3. Volumes montés :

`-v /srv/gitlab-runner/config:/etc/gitlab-runner`
Cela crée (ou utilise) un dossier sur ta machine hôte (Docker Desktop) à l’emplacement /srv/gitlab-runner/config (ou équivalent sur Windows).

Ce dossier contiendra la configuration persistante du runner (config.toml, etc.).

`-v /var/run/docker.sock:/var/run/docker.sock`
C’est ce qui permet au runner de lancer d'autres conteneurs Docker, en parlant directement avec le Docker Engine de Docker Desktop.

## Register gitlab-runner

GitLab CE v18
  1. Récupère le token d’enregistrement : dans l’interface de ton GitLab CE (ex. http://gitlab.local)

Accède à ton projet dans Settings > CI/CD > Runners
Puis clique sur "Expand" la section "Set up a specific Runner manually"
Copie le registration token

Le token d’enregistrement est différent du Personal Access Token (glpat...), et est fait uniquement pour enregistrer des runners.

### Enregistrement du runner

```
docker exec -it gitlab-runner gitlab-runner register \
  --url "http://host.docker.internal" \
  --token "glpat-xxxxx" \
  --name "runner-local" \
  --executor "docker" \
  --docker-image "mcr.microsoft.com/dotnet/sdk:7.0" \
  --tag-list "docker,local"
```

choix de l’exécuteur pour ton runner : docker

```
Registering runner... succeeded                     runner=GR1348941iVxSxgo4
Enter an executor: docker+machine, docker-autoscaler, instance, custom, shell, ssh, virtualbox, docker-windows, parallels, docker, kubernetes:
```

image par défaut : mcr.microsoft.com/dotnet/sdk:7.0

```
Enter the default Docker image (for example, ruby:2.7):
mcr.microsoft.com/dotnet/sdk:7.0
```

### Vérification du runner

 1. Ton runner est-il bien enregistré et actif ?
Va dans ton projet GitLab :
Menu > CI/CD > Runners

Tu dois voir ton runner dans la liste.
Il doit être marqué comme “active” et “online”.

Vérifie aussi qu’il est bien assigné à ton projet (pas juste "shared").

✅ 2. Le runner a-t-il les bons tags ?
Dans ton .gitlab-ci.yml, tu n’as mis aucun tags: dans les jobs, ce qui est OK si ton runner est sans tags.

dans le runner
```
# gitlab-runner list
Runtime platform                                    arch=amd64 os=linux pid=108 revision=4d7093e1 version=18.0.2
Listing configured runners                          ConfigFile=/etc/gitlab-runner/config.toml
build dotNet                                        Executor=docker Token=glrtr-bESNLz_xhyigEEAibghwTW86MQpwOjIKdDozCw.01.121ujupl7 URL=http://host.docker.internal
```

Mais si tu as ajouté des tags comme "dotnet" ou "docker" lors de l'enregistrement, tu dois les ajouter à tes jobs, par exemple :

```
build-job:
  stage: build
  tags:
    - build
    - dotnet
  script:
    - dotnet build --configuration Release --no-restore
Sinon GitLab ne trouve aucun runner correspondant.
```



https://docs.gitlab.com/runner/register/?tab=Docker
This GitLab instance does not provide any instance runners yet. Administrators can register instance runners in the admin area.



Intègre un workflow CI/CD dans .gitlab-ci.yml pour valider chaque MR


