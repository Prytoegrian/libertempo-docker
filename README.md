# Dockerisation des appplications Libertempo

[![BCH compliance](https://bettercodehub.com/edge/badge/Libertempo/libertempo-docker?branch=master)](https://bettercodehub.com/)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/ae69b553f18f48cfb7e56f79f686ce50)](https://www.codacy.com/app/Libertempo/libertempo-docker?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=Libertempo/libertempo-docker&amp;utm_campaign=Badge_Grade)

Ce readme n'a pas pour objectif de vous apprendre ce qu'est docker, la [doc officielle](https://docs.docker.com/) le fait bien mieux.

Par contre, il va vous permettre, en moins de 10 minutes (lecture comprise, no joke), d'avoir une instance de libertempo dockerisée. Il faut pour cela quelques prérequis :
* Docker et docker-compose installés sur votre poste
* Source de [Libertempo](https://github.com/wouldsmina/Libertempo) sur votre poste
* Source de ce repo sur votre poste
* Dans certains cas `apparmor` et `cgroup-lite` ([voir ticket](https://github.com/Prytoegrian/libertempo-docker/issues/5))

Vous avez tout cela, alors c'est parti !

## Configuration
La première des étapes est d'associer une IP à votre application dans le fichier hosts :
```
# /etc/hosts
localhost libertempo
```

Ensuite, vous devez préparer les sources pour créer un docker qui vous soit propre. Nous allons donc dire à docker de faire pointer les sources du site « Libertempo » en dehors de docker (comme un lien symbolique en somme).
La véritable localisation des sources de Libertempo se trouvera sur votre poste, de tel sorte que vous puissez travailler sans avoir à entrer dans docker en permanence.

Pour cela, copiez `docker-compose.yml.example` vers `docker-compose.yml` et modifiez la ligne du nouveau fichier `{path/vers/libertempo/web}`. Faites de même avec l'API, sinon supprimez la ligne.

Chez moi, puisque mes sources de libertempo se trouvent dans `/home/Prytoegrian/tardis/libertempo/web`, la ligne ressemblera donc à
```bash
    - /home/Prytoegrian/tardis/libertempo/web}:/var/www/web
```

## Installation de l'application
Une fois ceci fait, nous pouvons créer l'image de l'application Libertempo dockerisée. Exécutez donc le fichier d'installation :
```
make build
```
c'est lui qui va initialiser tout le serveur avec ses dépendances.

Il s'agit d'une installation d'un système unix somme toute normale (les lignes ne devraient pas vous surprendre), basée sur les ordres fournis par le fichier `Dockerfile`, un équivalent d'un `makefile`.

Enfin, la dernière étape :
```
make install
```
(*Le serveur MySQL est un peu capricieux à démarrer. Si l'opération échoue, recommencez*)

Tadaaa ! Une application libertempo pleinement opérationnelle. Vous pouvez vous en assurer en vous rendant sur `http://libertempo/`.

## Coupure et démarrage de l'application

Une fois l'application installée, son cycle de vie normale se résume en
```
make start
```
et

```
make stop
```

## Maintenance

Bien que non nécessaire à la vie de l'application, vous pourriez vouloir entrer dans le système dockerisé. Pour ce faire, saisissez :
```
make attach
```

Pour sortir du système dockerisé, faites `Ctrl-P Ctrl-Q` et vous voilà dehors. Tout simplement. Docker est toujours lancé, vérifiez votre navigateur, mais vous êtes à présent détaché.

## LDAP
LDAP tourne automatiquement sur le docker, mais ne possède rien de plus que l'utilisateur `admin:admin`. Pour ajouter d'autres utilisateurs, lancez `/opt/run/add_users_ldap.sh`.
Pour lister les utilisateurs, faites
```
ldapsearch -x -w admin -D cn=admin,dc=libertempo -b "ou=People,dc=libertempo"
```
