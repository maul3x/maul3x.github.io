###### Alexandre GUYOT - 12 Septembre 2014


# Bonnes pratiques de journalisation Apache Camel

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

Un même fichier pourra être utilisé par différente personne, par exemple un DBA ne sera pas intéressé par toutes les traces Java, le traitement des messages, les règles de filtrage etc. Ils veulent voir uniquement les requêtes SQL, leur paramètres et leur temps d'exécution. Les administrateurs systèmes eux sont un groupes d'individus devant lire un nombre très important de fichier obscure à la recherche  d'une information qui leur parle du type `Too many open files`. Ainsi les IOExceptions sont par définition la catégorie privilégier que ces personnes recherche au milieu d'un tas d'autre ligne de log pouvant être assimilié pour ces interlocuteurs à du chinois. 
Ainsi il faut tiré parti des fonctionnalités fournit par le framework de log pour créer des filtres évolué aux routes de journalisation.

### Abscence de hiérarchie dans les catégories de log

De nombreuse librairies n'utilisent qu'un sous ensemble de categorie de log si ce n'est une seule. Ceci est une idée complètement fausse, en y regardant de plus prêt on s'apperçoit que beaucoup de librairie utilise une cathégorie de log qui leur est propre (cf le framework Atomikos qui utilise à outrance la cathégorie du même nom : `atomikos`).
Dans le cas d'Apache Camel et autre solution middleware, il faut considérer :

* Utilisation de ses propres catégories, différent de celle d'un Framework, elles devront refléter une logique de médiation interne, sans détail technique. Effectivement même si vous êtes un dieu de la programmation des entrées de log peuvent vous induire en erreur si elles ne sont pas lié à une étape de processus bien définit.
* Assurer vous que tous les événements envoyé par votre processor, classe/bean, contexte ou route on la même structure de log. Ex : `org.maul3x.esb.finance` au lieu de `processor` ou `org.monpackage`. Soyer cohérent, créer une structure claire et s'y tenir (phase de conception capitale).
* Ne pas faire confiance a un nom de thread et ne pas l'utiliser comme catégorie. Effectivement ceux-ci peuvent changer après une update du camel core ou d'un composant.

Note : Les composants Camel ont un nom de package qui leur est propre, ceci vous permet de vous débarasser facilement de ceux dont les logs sont inutile pour votre besoin. 

### Données incomplètes dans les logs

Celle-ci est selon moi l'erreur la plus répandu. Le meilleur exemple étant la requête SQL généré par hibernate ne comportant pas la valeur des paramètres.

### Différents séparateurs dans une même ligne de log

todo

### Les entrées de log multi-ligne

todo

### L'écriture des logs après les actions bloquantes

todo




