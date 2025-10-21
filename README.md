# dreaddon-template

Ce dépôt est un modèle à utiliser comme base pour créer un addon de dremgr. Cf
le dépôt [dremgr sur github](https://github.com/PC-Scol/dremgr) pour les
détails.

Il faut créer un nouveau dépôt à partir de celui-ci, adapter la configuration,
puis déployer le dépôt de façon qu'il soit accessible publiquement s'il peut
être utilisé par d'autres établissement.

Ensuite, dans la configuration de dremgr, rajouter l'url du dépôt dans la
configuration:
~~~sh
ADDON_URLS="
...
PC-Scol/addonpublic.git
...
"
~~~
NB: le préfixe `https://github.com/` est rajouté automatiquement. On peut aussi
utiliser un serveur privé ou un autre serveur public.

Si c'est un addon privé, il faudra peut-être rajouter le compte et le mot de
passe pour l'accès dans l'url:
~~~sh
ADDON_URLS="
...
https://compte:motdepasse@gitprive.univ.fr/addonprive.git
...
"
~~~

Cet addon sera mis à jour depuis le dépôt et importé chaque jour à l'heure
configurée, c'est à dire 4h00 par défaut.

## Exécution

Pour rappel, le fichier `dreaddon.conf` permet de désigner les schémas à créer
ainsi que le mode de compatibilité.

Les fichiers sql ainsi que les scripts exécutables des répertoires `prepare`,
`vMM` et `updates` sont lancés pour mettre à jour la base de données

Les scripts du répertoire `notifications` sont lancés à la fin de l'import,
qu'il aie réussi ou non. Ils peuvent servir à envoyer des mails de rapport, à
notifier un service que les données ont été mises à jour, etc.

-*- coding: utf-8 mode: markdown -*- vim:sw=4:sts=4:et:ai:si:sta:fenc=utf-8:noeol:binary