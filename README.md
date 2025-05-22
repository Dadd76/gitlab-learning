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


