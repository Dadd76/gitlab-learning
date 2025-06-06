# gitlab-learning

Exercices pratiques et notes personnelles pour apprendre à utiliser GitLab avec Docker sur Windows.

## documentation 

https://www.youtube.com/playlist?list=PLn6POgpklwWrRoZZXv0xf71mvT4E0QDOF
https://about.gitlab.com/fr-fr/


## installation sous docker 

```
sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password

C:\Windows\System32\drivers\etc\host

127.0.0.1 gitlab.local

gitlab:
  environment:
    GITLAB_OMNIBUS_CONFIG: |
      external_url 'http://gitlab.local'
```



## ✅ Étapes pour forcer l’utilisation des Merge Requests

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

## gitlab-runner

🧱 1. L’image gitlab/gitlab-runner:latest est téléchargée
Elle est stockée localement dans Docker Desktop.

Tu peux la voir avec :

docker images
📦 2. Le conteneur gitlab-runner est créé
Le conteneur tourne dans l’environnement Docker Desktop (dans la VM Docker interne).

Tu peux le voir avec :

bash
Copier
Modifier
docker ps
ou via l’interface graphique de Docker Desktop.

📂 3. Volumes montés :
bash
Copier
Modifier
-v /srv/gitlab-runner/config:/etc/gitlab-runner
Cela crée (ou utilise) un dossier sur ta machine hôte (Docker Desktop) à l’emplacement /srv/gitlab-runner/config (ou équivalent sur Windows).

Ce dossier contiendra la configuration persistante du runner (config.toml, etc.).

bash
Copier
Modifier
-v /var/run/docker.sock:/var/run/docker.sock
C’est ce qui permet au runner de lancer d'autres conteneurs Docker, en parlant directement avec le Docker Engine de Docker Desktop.


docker run -d --name gitlab-runner --restart always -v /srv/gitlab-runner/config:/etc/gitlab-runner -v /var/run/docker.sock:/var/run/docker.sock gitlab/gitlab-runner:latest token : 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

Intègre un workflow CI/CD dans .gitlab-ci.yml pour valider chaque MR


