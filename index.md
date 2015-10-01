###### Oct. 13th, 2012

Bonnes pratiques de journalisation Apache Camel
-----------------------

Apache Camel implémente le MDC ([Mapped Diagnostic Context](http://camel.apache.org/mdc-logging.html)) qui permet d'enrichir automatiquement les logs avec des données de contexte. De plus Apache Camel implémente un [composant log](http://camel.apache.org/log) randant extrèmement simple l'envoie de log. Ensemble ils peuvent fournir un socle de monitoring d'activité ne nécesitant aucun outil tierce ou base de donnée.

# Anti Patterns de journalisation

Sed nisl lorem, molestie et dapibus non, adipiscing non est. Duis nec sapien quam, ac fermentum arcu. Donec blandit turpis lobortis erat vehicula elementum. Proin ut tortor eu nulla iaculis aliquam. Etiam ullamcorper, metus nec iaculis laoreet, orci metus lobortis ante, eget pretium tortor orci vel lorem. Duis vitae velit quam, nec sagittis dolor. Fusce tincidunt felis quis dolor eleifend eget porttitor tellus lobortis. Nulla id metus ut lorem imperdiet posuere imperdiet non diam. Ut imperdiet ullamcorper placerat. Nam porttitor pellentesque lectus id porttitor. Integer pretium, erat at sodales semper, metus urna venenatis leo, eu semper felis velit eget risus. Nulla quis est turpis, ut lacinia velit.
![](img/royal1.jpg)

Curabitur egestas arcu quis mi aliquam adipiscing. Mauris at libero quis nulla malesuada feugiat sit amet at lacus. Nam eu elit ante, vitae pretium nulla. Donec bibendum, nulla vel mollis fringilla, nunc quam interdum metus, sed luctus nisl massa ultricies enim. Ut sollicitudin consectetur velit. Fusce mattis nunc eu magna dignissim vitae blandit mi mattis. Donec sed est lorem, et feugiat sem. Aliquam et gravida turpis. Suspendisse potenti. Donec ultricies felis nec nisl convallis imperdiet. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aenean nec orci elit. Donec facilisis dignissim nunc ac tincidunt. Fusce adipiscing nunc fringilla arcu aliquet lobortis ultrices urna hendrerit. Pellentesque et mauris dolor, vel auctor tortor.

Suspendisse potenti. Praesent placerat libero et purus dapibus ut tincidunt neque iaculis. Sed ligula diam, tempor ornare fermentum quis, placerat eget risus. Nam ut ultrices ipsum. Aliquam consequat laoreet lorem, at mattis nisl facilisis et. Integer ullamcorper blandit lacus. Sed sollicitudin arcu ut lectus tincidunt eget dapibus ipsum adipiscing. Etiam libero mi, tempus nec ultricies ornare, tempor ut lectus. Cras rhoncus augue id urna interdum sed blandit odio bibendum. Duis sem tellus, sagittis eget ornare vitae, ullamcorper at orci. Donec eu erat vitae ligula ullamcorper viverra. Phasellus sagittis sollicitudin ante. In volutpat purus non odio pretium dignissim. Vestibulum ut felis ipsum, tempor tincidunt purus. Sed libero sapien, commodo in suscipit eu, auctor et nisl.

![](img/royal2.jpg)
![](img/royal3.jpg)
