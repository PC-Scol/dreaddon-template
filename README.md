# dreaddon-template

Ce dépôt est un modèle à utiliser comme base pour créer un addon de DRE, à
utiliser conjointement avec [dremgr](https://github.com/PC-Scol/dremgr)

Il faut créer un nouveau dépôt à partir de celui-ci, adapter la configuration,
puis déployer le dépôt de façon qu'il soit accessible publiquement s'il peut
être utilisé par d'autres établissement.

Ensuite, dans la configuration de dremgr, rajouter l'url du dépôt dans la
configuration:
~~~sh
ADDON_URLS="
...
https://github.com/PC-Scol/addonpublic.git
...
"
~~~

Si c'est un addon privé, il faudra sans doute rajouter le compte et le mot de
passe pour l'accès dans l'url:
~~~sh
ADDON_URLS="
...
https://compte:motdepasse@gitprive.univ.fr/addonprive.git
...
"
~~~

Cet addon sera mis à jour depuis le dépôt et importé chaque jour à la date
prévue.

## Configuration

la configuration de l'addon se fait dans le fichier `dreaddon.conf`, qui est
obligatoire pour que le dépôt soit considéré comme un addon DRE.

Les variables suivantes peuvent être configurées:

`SCHEMAS`
: cette variable liste le ou les schémas à créer et provisionnés par cet addon.

  la création des schémas se fait par le script d'import de dremgr sur la base
  de cette variable, mais il est possible aussi de faire créer le schéma par
  l'addon, surtout si la création se fait avec des paramètres particuliers.

  dans tous les cas, il faut lister les schémas, parce que ça permet au script
  d'import de savoir quel schémas supprimer avant la recréation quotidienne.

`COMPAT`
: indiquer les versions de DRE avec lesquelles cet addon est compatible. seules
  deux valeurs sont autorisées: `all` et `vxx`

  La valeur de ce paramètre influe sur les répertoires depuis lesquels sont
  chargés les scripts SQL

## Scripts SQL

L'importation des schémas DRE se fait de cette manière:

* Suppression des schémas livrés par DRE
* Suppression des schémas mentionnés dans la variable `SCHEMAS` de
  `dreaddon.conf`
* Importation des dumps du jour livrés par DRE
* Traitements des scripts SQL des addons, dans l'ordre mentionné dans la
  configuration de dremgr

Pour chaque addon, les scripts SQL sont lancés depuis les répertoires suivants:

* les scripts du répertoire `Aprepare` sont lancés après que tous les schémas
  livrés par DRE ont été importés. Après ces scripts, si ce n'est pas déjà le
  cas, les schémas mentionnés dans la variable `SCHEMAS` de `dreaddon.conf` sont
  créés.
* En mode de compatibilité `vxx`, les scripts du répertoire nommé `vMM` où MM
  est la version majeure de la base DRE sont lancés.

  Les scripts à lancer sont cherchés dans le répertoire de numéro égal ou
  inférieur le plus proche. par exemple, si la version majeure de la base DRE
  est 24, les scripts sont chargés depuis le répertoire `v24`. Si celui-ci
  n'existe pas, ils sont chargés depuis le répertoire `v23` et ainsi de suite.
  Si aucun répertoire n'est trouvé, alors l'addon est considéré comme
  incompatible avec la version de DRE installée, et il est ignoré.

  Une autre façon de le dire est que si le répertoire `vMM` existe, il est
  utilisé par toutes les versions de DRE égales ou supérieures à `MM`, jusqu'à
  ce qu'une version spécifique soit livrée.
* l'accès à toutes les tables des schémas est donné à l'utilisateur `reader`
  configuré dans dremgr. puis les scripts du répertoire `Zfinalize` sont lancés.

Cette section parle de scripts SQL, mais en réalité, les fichiers `*.sql` et
`*.sh` (s'ils sont exécutables) sont considérés

## Documentation

Si le répertoire `documentation` existe, son contenu direct est mis à
disposition du frontal de dremgr

En mode de compatibilité `vxx`, si le répertoire `documentation/vMM` existe (où
MM est la version majeure de DRE), son contenu direct est aussi mis à
disposition du frontal de dremgr

Cela permet de fournir aux utilisateurs la version la plus à jour correspondant
à la base DRE installée

## Exploitation

Le mode de compatibilité `vxx` permet de préparer à l'avance les scripts et
documentation éventuelles pour une version donnée. En mettant à jour le dépôt
avec ces informations, les scripts appropriés sont disponibles et utilisés
aussitôt que la base DRE est dans la bonne version.

-*- coding: utf-8 mode: markdown -*- vim:sw=4:sts=4:et:ai:si:sta:fenc=utf-8:noeol:binary