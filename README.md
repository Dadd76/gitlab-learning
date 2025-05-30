# gitlab-learning
Exercices pratiques et notes personnelles pour apprendre à utiliser GitLab avec Docker sur Windows.

sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password

C:\Windows\System32\drivers\etc\host

127.0.0.1 gitlab.local

gitlab:
  environment:
    GITLAB_OMNIBUS_CONFIG: |
      external_url 'http://gitlab.local'

https://www.youtube.com/playlist?list=PLn6POgpklwWrRoZZXv0xf71mvT4E0QDOF


✅ Étapes pour forcer l’utilisation des Merge Requests
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

Intègre un workflow CI/CD dans .gitlab-ci.yml pour valider chaque MR


