# Pourquoi un champ fields ?

En API REST le paramètre fields permet de filtrer les champs de la réponse que l'on souhaite. Ce besoin est apparu avec la multiplication des devices mobile, n'ayant pas toujours une bonne couverture réseau ou un forfait data réduit nécessitant donc une optimisation des requête effectuée sur les API.

# Quels sont les solutions à première vu ?

Dans ce cadre on peut se dire qu'une bonne solution serait de ne pas retourner les champs pas demandé en mettant en place un filtre de donnée au niveau reverse proxy. L'avantage étant de ne pas avoir de modification de code au niveau de l'api REST et que ce mécanisme marcherais également sur un cache. Cependant je ne trouve pas simple l'implémentation d'une telle solution, build d'un module apache ou autre...

De plus cette approche n'optimise pas tellement notre code serveur puisque dans tous les cas on retourne l'objet entier pour n'en conserver que quelques éléments. Une approche complète serait de construire une requête dynamiquement, mais si on veut respecter les bonnes pratiques et éviter les injections SQL il est préférable de trouver un compromis. Pour moi la bonne approche consiste à ne sérialiser que les fields que l'on souhaite retourner.

# Comment l'implémenter dans la réalité ?

Bon maintenant qu'on à choisi notre aproche, il serait étrange que cette features n'existe pas déjà dans la stack Java. Quelque requête google et github plus tard... Je découvre que [Jackson](http://stackoverflow.com/questions/9314735/how-to-return-a-partial-json-response-using-java) intègre une fonction de filtrage via l'annotation [@JsonFilter](http://wiki.fasterxml.com/JacksonFeatureJsonFilter).

Bon elle ne fait pas exactement ce que l'on souhaite mais pas loin, de plus elle impose que les classes porte toute une annotation @JsonFilter. Très peu pour moi je ne vais pas rajouter cela sur toutes les classes de mon modèle. L'astuce que je choisie donc est de l'ajouter dynamiquement via un custom Annotation Introspector, celui-ci dira amène à toutes les classes de mon modèle comme si elle portait une annotation `@JsonFilter("DynamicFieldsFilter")`.

Maintent d'autre problème s'impose, comment injecté le filter dans l'objectMapper de mon conteneur JEE ?
TODO cf EME

Ok mais il faut s'assurer que l'ensemble soit threadsafe.
TODO cf EME

Tada !!!!

