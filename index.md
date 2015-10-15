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

Celle-ci est selon moi l'erreur la plus répandu. Le meilleur exemple étant la requête SQL généré par hibernate ne comportant pas la valeur des paramètres. Pour obtenir l'ensemble des données relative à l'exécution d'une requête SQL dont les fameux paramètres, on est obligé d'utiliser 2 catégories de log différentes qui n'ont pas de lien d'héritage entre elles. Si le niveau de log TRACE vous convient pour le package org.hibernate, vous pouvez passer cette exemple.
Si vous avez tous les parametres et les requêtes SQL ensemble dans les logs. Même si la ligne de log sera consomera un max de temps, vous serez sûr que vous verrez les paramètres pour cette requête SQL, et pas ceux d'une autre.

### Différents séparateurs dans une même ligne de log

Pour ma part, j'aime les lignes de log complex avec des pipes, des points, des paranthèses, des crochets, etc. C'est tellement geek, on croirait pouvoir lire la matrice ! Bon pour nos yeux, en temps que développeurs averti, ça passe (même si ça fini toujours par piquer un peu) ! Mais pour utiliser des outils de parsing de log et pour les dinosaures de l'exploitation, c'est moins évident. Et comme c'est toujours aux développeurs (l'être le plus bat de l'échelle) d'obéir aux ordre, il va falloir rendre ça plus éléguant, avec un pattern de parsing simple et robuste, avec un séparateur unique. D'ailleurs quelques outils commerciaux nécessite un format particulier, construiser votre pattern en connaissance de cause, utilisé le séparateur privilégier par votre outils d'exploitation ou utilisez un appender différent. Même si cela semble redondant, garder un log claire, n'affecter pas votre pattern de log pour un besoin spécifique qui vous rendra au final la lecture plus complexe et vous fera donc perdre du temps en diagnostique.

### Les entrées de log multi-ligne

Imaginé une requête SQL de 5 lignes, c'est pas si énorme ? Mais que ce passe t'il quand vous avez un contenu de 200Ko (ou plus) à logger régulièrement ? Votre système de fichier va vite empatire, et l'espace disque fondre comme neige au soleil à la surface de mercure. De manière plus générale, des entrées de logs sur plusieurs ligne complexifie le parsing, mais rendent aussi plus long et compliqué la lecture. Si vous avez besoin de conserver une trace de messages volumineux, ne le faite pas dans les fichiers de log, ce n'est pas l'endoit adapté. Privilégié un NMR si vous êtes en OSGi ou une file JMS avec un message store derrière (cf la [solution ActiveMQ](http://activemq.apache.org/amq-message-store.html)). Vous pouvez également contruire un mécanisme spécifique à votre besoin, en utilisant un design pattern d'asynchroniqme du type [wire tap](http://camel.apache.org/wire-tap) pour limiter tout impact sur la performance.

### L'écriture des logs après les actions bloquantes

Le dernier Anti-pattern traité dans cet article. Souvener-vous que les logs sont produits par un bout de code qui devrait introduire une opération. Si vous n'êtes pas d'accord avec ce point, pensé à notifier les personnes sur votre façon de logger au préalable. La plus part d'entre elles s'attendent à trouver trace d'un traitement avant qu'une erreur arrive :

1. Ce qu'on traite : Réception du message avec pour ID : ...
2. L'erreur obtenu : Exception pendant la transformation du message
3. L'action de recouvrement déclanché : Message transmit à la DeadLetter



## Les bonnes pratiques de journalisations Apache Camel

Apache Camel utilise `slf4j` comme librairie de logging. C'est une facade générique pour de nomreuses librairies de logging. Vous n'avez pas à l'utiliser directement dans vos projet, vous préfererez un librairie de plus haut niveau comme `Log4j` ou `commons-logging`, mais c'est toujours intéressant d'avoir moins de dépendance et un classpath épuré. Nous n'allons pas critiquer le choix de `slf4j` ici, ce n'est pas l'objectif, donc inutile d'ouvrir le débat. 

### Nommer vos éléments

Si vous avez déjà vécu l'exploitation d'un outil middleware en prod, vous voyer l'intérêt. Pour les autres (les barbus du code), vous savez qu'il est impossible de connecter votre IDE et débuggeur favori sur un environnement de prod, ainsi le seul lien pour savoir quel éléments crie à l'aide dans vos log, c'est de leur donner un petit nom bien parlant. Voilà pourquoi tout Camel context doit avoir un nom. D'ailleurs du point de vue des exploitants : tout doit avoir un nom précis. Si vous avez une console JMX pour faire quelque tache d'administration, le nommage natif de camel (ex : 123-camel-45) ne vous sera d'aucune aide. TODO ajouter une image pour illustrer !

### Utilisé le MDC (Mapped Diagnostic Context)

Le MDC est une passerelle entre votre application et le framework de logging. Voyer le comme un thread local. Vous pouvez définir des variables spécifique à votre aplication et les réutiliser pour créer un format de log riche ou encore séparrer vos fichier de log par application ou cas d'usage. Cette fonctionalité est désactivé par défaut dans Camel, mais une simple properties permet de [l'activer](http://camel.apache.org/mdc-logging.html). Donnant ainsi accès à quelque variable interne de Camel :

* camel.exchangeId
* camel.messageId
* camel.correlationId
* camel.transactionKey
* camel.routeId
* camel.breadcrumbId
* camel.contextId

Chaque variable de cette liste peut être utiliser dans votre format de log ou appender. Exemple :
`log4j.appender.out.layout.ConversionPattern=%d{ABSOLUTE} | %-5.5p | %X{camel.contextId} %X{bundle.version} | %X{routeId} %X{exchangeId} | %m%n`

Ce pattern poura produire la ligne de log suivante :
`23:56:12,158 | INFO  | database-batch 2.8.0.fuse-01-06 | jms-inbound-hr | ID-Code-House-local-60526-1330335502920-25-33 | Received message`

Pour moi le fichier de configuration de log, doit être quelque chose d'extrèmement simple à lire :

    log4j.appender.integrationProcess=org.apache.log4j.sift.MDCSiftingAppender
    log4j.appender.integrationProcess.key=camel.contextId
    log4j.appender.integrationProcess.default=unknown
    log4j.appender.integrationProcess.appender=org.apache.log4j.RollingFileAppender
    log4j.appender.integrationProcess.appender.layout=org.apache.log4j.PatternLayout
    log4j.appender.integrationProcess.appender.layout.ConversionPattern=%d{ABSOLUTE} | %-5.5p | %X{camel.routeId} %X{bundle.version} | %X{camel.exchangeId} | %m%n
    log4j.appender.integrationProcess.appender.file=${karaf.data}/log/mediation-$\\{camel.contextId\\}.log
    log4j.appender.integrationProcess.appender.append=true
    log4j.appender.integrationProcess.appender.maxFileSize=1MB
    log4j.appender.integrationProcess.appender.maxBackupIndex=10
    log4j.category.com.mycompnany.camel_toys.hr=INFO, integrationProcess

Par défaut, quand le camel.contextId n'est pas définit, les entrées de log apparaiteront dans un fichier  data/log/mediation-unknown.log. Mais pour ceux qui ont bien un context id de définit, le fichier sera beaucoup plus explicite data/log/mediation-database-batch.log :

    00:38:42,386 | INFO  | jms-inbound-hr 2.8.0.fuse-01-06 | ID-Code-House-local-51055-1330383822002-4-67 | Received message with business key XYZ.
    00:38:43,387 | INFO  | jms-inbound-hr 2.8.0.fuse-01-06 | ID-Code-House-local-51055-1330383822002-4-67 | Saving message in database
    00:38:44,388 | INFO  | jms-inbound-hr 2.8.0.fuse-01-06 | ID-Code-House-local-51055-1330383822002-4-67 | Processing of message with business key XYZ complete.
    00:38:44,389 | INFO  | jms-inbound-hr 2.8.0.fuse-01-06 | ID-Code-House-local-51055-1330383822002-4-68 | Received message with business key ZYX.
    00:38:45,390 | INFO  | jms-inbound-hr 2.8.0.fuse-01-06 | ID-Code-House-local-51055-1330383822002-4-68 | Saving message in database
    00:38:45,391 | ERROR | jms-inbound-hr 2.8.0.fuse-01-06 | ID-Code-House-local-51055-1330383822002-4-68 | Processing of message with business key ZYX failed.

Comme vous pouvez le voir, ce fichier ne contient que les entrées de log spécifique à un processus et il n'est pas influencé par des détails techniques. Une categorie est évincé car elle n'est pas constructive dans ce contexte (elle l'est pour le développeur mais pas pour l'exploitant). De plus vous n'avez pas à extraire une clé métier, vous pouvez simplement utiliser le header BreadcrumbId ou la property CorrelationId.

### Les processors embarqué dans la route

En Java DSL il est commun de voir un processor en plein milieu d'une route. C'est même inévitable dès qu'un développeur découvre cette possibilité, il ne cherche même plus le composant EIP adéquat et privilégit un bout de code bien moche au milieu de la route. C'est d'ailleurs une des raisons qui me fait préférer la modélisation de route en XML plutôt qu'en Java DSL, mais c'est une nouvelle fois un autre sujet.

Si jamais vous venez à les ustiliser sacher que votre catégorie devra contenir un sign `$`, ex : `my.company.RouteBuilderExt$1`. Déjà n'implémenter pas de chose complexe dans ces boites magiques, mais si vous avez besoin d'un peu plus qu'une transformation de chaine de caractère ou l'ajout d'un simple header et que vous avez besoin de logger ça. Alors créer une classe séparrer et souvenez vous de la hiérarchie des catégorie de log, n'utiliser pas une catégorie de log RouteBuilder. Si l'implémentation de la logique du processor prend la magorité des lignes de code du constructeur de la route, séparrer le. Même chose pour les strategies d'aggregation et les extenstion `org.apache.camel.spi`.

    package com.mycompnany.camel_toys.hr;
    public class Route extends RouteBuilder {
      
        Log log = LogFactory.getLog(Route.class);
      
        @Override
        public void configure() throws Exception {
            from("")
                .onException(Exception.class)
                    .process(new Processor() {
                        @Override
                        public void process(Exchange exchange) throws Exception {
                            org.apache.camel.Message out = exchange.getOut();
                            out.setHeader(org.apache.cxf.message.Message.RESPONSE_CODE, new Integer(500));
                            log.error("Unexpected exception", exchange.getException());
                        }
                    }
                })
                .end()
                .to("")
        }
    }
    
Il est préférable d'avoir une classe séparée :

    package com.mycompnany.camel_toys.hr; // or .processor if we have few or logic is complex
    public class ExceptionProcessor implements Processor {
        // slf4j!
        private Log log = LoggerFactory.getLogger(ExceptionProcessor.class); 
        @Override
        public void process(Exchange exchange) throws Exception {
            org.apache.camel.Message out = exchange.getOut();
            out.setHeader(org.apache.cxf.message.Message.RESPONSE_CODE, new Integer(500));
            log.error("Unexpected exception", exchange.getException());
        }
    }
    
### Utilisation du composant camel-log

