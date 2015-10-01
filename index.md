###### Oct. 13th, 2012

Bonnes pratiques de journalisation Apache Camel
-----------------------

Apache Camel implémente le MDC ([Mapped Diagnostic Context](http://camel.apache.org/mdc-logging.html)) qui permet d'enrichir automatiquement les logs avec des données de contexte. De plus Apache Camel implémente un [composant log](http://camel.apache.org/log) randant extrèmement simple l'envoie de log. Ensemble ils peuvent fournir un socle de monitoring d'activité ne nécesitant aucun outil tierce ou base de donnée.

## Anti-Patterns de journalisation

Avant toutes choses, vous pouvez prendre connaissance du très bon article sur [les anti-patterns de journalisation](http://gojko.net/2006/12/09/logging-anti-patterns/) écrit par Gojko Adzic. Cet article met en exergue comment assouvir les problèmes courant liés à la journalisation :

* Un fichier unique pour tous le monde
* Absence de hiérarchie dans les catégories de log
* Données incomplètes dans les logs (stacktrace tronqués ou Requête SQL sans la valeur des paramètres)
* Différents séparateurs dans une même ligne de log
* Différents format de message incompatible dans un même fichier de log
* Les entrées de log multi-ligne
* L'écriture des logs après les actions bloquantes (log d'une requête SQL après l'exécution de celle-ci)

Nous allons maintenant regarder comment ces anti-patterns peuvent être traiter en Java et plus particulièrement avec Apache Camel.

### Un fichier unique

todo

### Abscence de hiérarchie dans les catégories de log

todo

### Données incomplètes dans les logs

todo

### Différents séparateurs dans une même ligne de log

todo

### Les entrées de log multi-ligne

todo

### L'écriture des logs après les actions bloquantes

todo




