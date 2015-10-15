# Designer une API Rest

## Introduction
Lorsque l’on souhaite concevoir une API, on est rapidement confronté à la problématique du « design d’API ». Ce point constitue un enjeu majeur, dans la mesure où une API mal conçue ne sera vraisemblablement peu ou pas utilisée par nos clients : les développeurs d’applications.

La mise en oeuvre d’une API à l’état de l’Art nécessite de prendre en compte:

non seulement les principes substantiels des API RESTful issus de la littérature de référence (Roy Fielding, Leonard Richardson, Martin Fowler, spécifications HTTP…)
mais également les bonnes pratiques utilisées par les API des “Géants du Web”.
En effet, il arrive que deux approches s’opposent : celle des “puristes”, qui militent pour défendre les principes RESTful sans concession, et celle des “pragmatiques” qui privilégient une approche plus pratique, pour que leur API soit fonctionnelle entre les mains d’utilisateurs réels. Bien souvent, la juste réponse se situe au milieu.

Par ailleurs, la phase de conception/design d’une API REST soulève un ensemble de problématiques pour lesquelles les réponses ne sont pas encore unanimes. Les bonnes pratiques REST sont toujours en voie de consolidation et rendent la démarche passionnante.

Afin de faciliter et d’accélérer la mise en oeuvre des API, nous proposons nos convictions, issues de nos expériences autour du sujet API

DISCLAIMER : Cet article constitue un recueil de bonnes pratiques, qui peuvent bien entendu être discutées. Nous vous invitons à participer à la réflexion et à challenger ces choix sur notre blog

## Concepts généraux
KISS – « Keep it simple, stupid »

Un des objectifs d’une stratégie API vise à “s’ouvrir” sur le WWW, pour toucher un public de développeurs le plus large possible. Il est donc primordial que l’API soit la plus simple possible et auto-descriptive, de manière à ce que l’utilisateur ait à consulter la documentation le moins possible. On parle alors d’affordance : c’est à dire de la capacité de l’API à suggérer son utilisation.

Lors du design d’API, il convient de garder à l’esprit les principes suivants :

La sémantique d’une API doit être intuitive, qu’il s’agisse de l’URI, du payload de la requête ou des données retournées : un utilisateur devrait pouvoir exploiter l’API en ayant recours le moins possible à la documentation de l’API.
Les termes utilisés doivent être usuels et concrets : clients, orders, addresses, products,… et non tirés d’un “jargon fonctionnel”.
Il convient de ne pas proposer aux utilisateurs de pouvoir faire quelque chose de plusieurs manières différentes.
➡ Quel “Blender” vous parait être le plus simple d’utilisation

L’API est “designée” pour les clients : les développeurs, et non pour les “data” (représentation interne des données de l’entreprise). L’API exposée doit exposer des fonctionnalités simples qui répondent aux besoin du client. Une erreur fréquemment rencontrée est de designer son API à partir d’un modèle de données existant, qui est souvent complexe.
➡ Quel “Blender” vous parait être le plus simple d’utilisation

Enfin, en phase de conception, il convient de se focaliser en premier lieu sur les “uses-cases” principaux, et traiter les cas exceptionnels dans un second temps.

## Exemples cURL

Chez les Géants du Web, les exemples cURL sont largement utilisés pour illustrer les appels vers leur API :

https://developer.github.com/v3/
https://developers.google.com/youtube/v3/live/authentication#client-side-apps
https://developer.paypal.com/docs/api/
https://developers.facebook.com/docs/graph-api/making-multiple-requests
https://www.dropbox.com/developers/blog/45/using-oauth-20-with-the-core-api
http://instagram.com/developer/endpoints/likes/
http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AESDG-chapter-instancedata.html
…
Nous préconisons d’illustrer systématiquement vos appels d’API via des exemples cURL dans la documentation de l’API, qui peuvent être utilisés en copier-coller, pour lever toute ambiguïté concernant les modalités d’appel.

Exemple

    CURL –X POST \
    -H "Accept: application/json" \
    -d '{"state":"running"}' \
    https://api.fakecompany.com/v1/clients/007/orders
    
## Granularité Moyenne

Si la théorie “une ressource = une URL” pousse à découper l’ensemble des ressources, il nous semble important de garder une limite raisonnable dans ce découpage.

Exemple : les informations d‘une personne contiennent une adresse courante qui elle-même contient un pays.

Il s’agit d’éviter d’avoir 3 appels à réaliser :

1
2
3
4
5
6
7
8
9
10
11
CURL https://api.fakecompany.com/v1/users/1234
< 200 OK
< {"id":"1234", "name":"Antoine Jaby", "address":"https://api.fakecompany.com/v1/addresses/4567"}
 
CURL https://api.fakecompany.com/addresses/4567
< 200 OK
< {"id":"4567", "street":"sunset bd", "country": "http://api.fakecompany.com/v1/countries/98"}
 
CURL https://api.fakecompany.com/v1/countries/98
< 200 OK
< {"id":"98", "name":"France"}
Alors que ces informations sont généralement utilisées ensemble. Ceci peut engendrer des problèmes de performance lié à un trop grand nombre d’appels.

Inversement, si on agrège trop de données a priori, on surcharge inutilement les appels, avec le risque de générer des échanges trop verbeux.

D’une manière générale, exposer une API avec une granularité optimale est souvent culturel, et le fruit de l’expérience sur les problématiques de design d’API. Dans le doute, il faut éviter de cibler ni trop gros, ni trop fin.

Plus concrètement, nous préconisons :

De ne grouper que les ressources qui seront accédées à la suite de manière quasi systématique.
De ne pas grouper les collections pouvant avoir de nombreux composants. Par exemple, la liste des emplois courants sont limités (une personne ne peut pas cumuler beaucoup plus que 2 ou 3 emplois à temps partiel), par contre, la liste des expériences professionnelles peut être très longue.
De se limiter à deux niveaux d’imbrication :
/v1/users/addresses/countries

## Noms de domaines des API

Chez les Géants du Web, on constate que les domaines sont disparates. Certains utilisent plusieurs domaines ou sous-domaines pour leurs API : c’est notamment le cas de Dropbox.

➡ Chez les Géants du Web

API	Domaines / Sous domaines	Exemples d’URI
Google	https://accounts.google.com
https://www.googleapis.com
https://developers.google.com	https://accounts.google.com/o/oauth2/auth
https://www.googleapis.com/oauth2/v1/tokeninfo
https://www.googleapis.com/calendar/v3/
https://www.googleapis.com/drive/v2
https://maps.googleapis.com/maps/api/js?v=3.exp
https://www.googleapis.com/plus/v1/
https://www.googleapis.com/youtube/v3/
https://developers.google.com
Facebook	https://www.facebook.com
https://graph.facebook.com
https://developers.facebook.com	https://www.facebook.com/dialog/oauth
https://graph.facebook.com/me
https://graph.facebook.com/v2.0/{achievement-id}
https://graph.facebook.com/v2.0/{comment-id}
https://graph.facebook.com/act_{ad_account_id}/adgroups
https://developers.facebook.com
Twitter	https://api.twitter.com
https://stream.twitter.com
https://dev.twitter.com	https://api.twitter.com/oauth/authorize
https://api.twitter.com/1.1/statuses/show.json
https://stream.twitter.com/1.1/statuses/sample.json
https://dev.twitter.com
GitHub	https://github.com
https://api.github.com
https://developer.github.com	https://github.com/login/oauth/authorize
https://api.github.com/repos/octocat/Hello-World/git/commits/7638417db6d59f3c431d3e1f261cc637155684cd
https://developer.github.com
Dropbox	https://www.dropbox.com
https://api.dropbox.com
https://api-content.dropbox.com
https://api-notify.dropbox.com	https://www.dropbox.com/1/oauth2/authorize
https://api.dropbox.com/1/account/info
https://api-content.dropbox.com/1/files/auto/
https://api-notify.dropbox.com/1/longpoll_delta
https://api-content.dropbox.com/1/thumbnails/auto/
https://www.dropbox.com/developers
Instagram	https://api.instagram.com
http://instagram.com	https://api.instagram.com/oauth/authorize/
https://api.instagram.com/v1/media/popular
http://instagram.com/developer/
Foursquare	https://foursquare.com
https://api.foursquare.com
https://developer.foursquare.com	https://foursquare.com/oauth2/authenticate
https://api.foursquare.com/v2/venues/40a55d80f964a52020f31ee3
https://developer.foursquare.com
Afin de normaliser les noms de domaines, dans un souci d’affordance, nous préconisons d’utiliser uniquement trois sous-domaines pour la production :

API – https://api.{fakecompany}.com
OAuth2 – https://oauth2.{fakecompany}.com
Portal developer – https://developers.{fakecompany}.com

L’objectif est que le développeur, qui consomme l’API, puisse déterminer de manière intuitive quel domaine appeler, en fonction qu’il souhaite :

Appeler l’API
Récupérer un jeton pour appeler l’API (via OAuth2)
Consulter le “portail developer” de l’API
Par ailleurs, Paypal propose un environnement “sandbox” pour son API, très pratique pour pouvoir réaliser des tests “Try-It” : https://developer.paypal.com/docs/api/

Nous proposons d’utiliser deux sous-domaines spécifiques pour une plate-forme de tests :

OAuth2 – https://oauth2.sandbox.{fakecompany}.com
API – https://api.sandbox.{fakecompany}.com

## Sécurité

Deux protocoles sont généralement utilisés pour sécuriser les API REST :

OAuth1 : http://tools.ietf.org/html/rfc5849
OAuth2 : http://tools.ietf.org/html/rfc6749
➡ Chez les Géants du Web et dans les DSI

OAuth1	OAuth2
Twitter, Yahoo, flickr, tumblr, Netflix, myspace, evernote,…	Google, Facebook, Dropbox, GitHub, amazone, Intagram, LinkedIn, foursquare, salesforce, viadeo, Deezer, Paypal, Stripe, huddle, boc, Basecamp, bitly,…
MasterCard, CA-Store, OpenBankProject, intuit,…	AXA Banque, Bouygues telecom,…
Nous préconisons de sécuriser votre API via le protocole OAuth2.

Contrairement à OAuth1, OAuth2 permet de gérer l’authentification et l’habilitation des ressources par tout type d’application (native mobile, native tablette, application javascript, application web de type serveur, application batch/back-office, …) avec ou sans consentement de l’utilisateur propriétaire des ressources.
OAuth2 est le standard de sécurisation des API : proposer un protocole marginal freinerait vraisemblablement l’adoption de votre API.
D’autre part, les problématiques de sécurisation des ressources sont complexes, et une solution propriétaire entraînerait à coût sur des risques non négligeables.
Nous proposons d’implémenter la préconisation de Google pour le flow OAuth2 implicit relative à la validation du token :

https://developers.google.com/accounts/docs/OAuth2UserAgent#validatetoken
http://en.wikipedia.org/wiki/Confused_deputy_problem
Nous préconisons d’utiliser systématiquement le protocole HTTPS pour tous les accès vers :

les providers OAuth2
les providers d’API
Un excellent test pour valider la bonne implémentation ou mise en oeuvre de votre solution OAuth2 consiste à réaliser un appel avec le même code client sur votre API, et sur l’API Google par exemple, en ayant à changer uniquement les noms de domaine des serveurs OAuth2 et API.

## URIs
Noms > verbes

Pour décrire vos ressources, nous préconisons d’utiliser des noms concrets, pas de verbe.

En informatique, nous utilisons depuis longtemps les verbes pour exposer des services avec une approche RPC, par exemple :

getClient(1)
creerClient()
majSoldeCompte(1)
ajoutetProduitDansCommande(1)
effacerAdresse(1)
…
Mais dans une approche RESTful, nous préconisons d’utiliser :

GET /clients/1
POST /clients
PATCH /accounts/1
PUT /orders/1
DELETE /addresses/1
…
Un des objectifs fondamentaux d’une API REST est précisément d’utiliser HTTP comme protocole applicatif, pour homogénéiser et faciliter les interactions entre SI et pour ne pas avoir à façonner un protocole « maison » de type SOAP/RPC ou EJB, qui a l’inconvénient de “réinventer la roue” à chaque fois.

Il convient donc d’utiliser systématiquement les verbes HTTP pour décrire les actions réalisées sur les ressources (voir rubrique CRUD).

En outre, l’utilisation des verbes HTTP rend l’API intuitive et permet d’éviter que le développeur n’ait à consulter une documentation verbeuse pour comprendre comment manipuler les ressources, favorisant ainsi l’affordance de l’API.

En pratique, le développeur client est en général équipé d’un outillage (bibliothèques et framework) qui permet de générer des requêtes HTTP avec les verbes adéquates, à partir d’un modèle objet, lorsque ce dernier est mis à jour.

Pluriel > singulier

La plupart du temps, les Géants du Web restent consistants, entre les noms de ressource au singulier et les noms au pluriel. L’enjeu principal est effectivement de ne pas les mélanger. En effet, varier les noms de ressources entre pluriel et singulier freine “l’explorabilité” de l’API.

Par ailleurs, les noms de ressources nous semblent être plus naturel au pluriel afin d’adresser de manière consistante les collections et instances de ressources.

Nous préconisons donc d’utiliser le pluriel pour gérer les deux type de ressources :

Collection de ressources : /v1/users
Instance d’une ressource : /v1/users/007
Pour la création d’un utilisateur par exemple, on considère que POST /v1/users est l’invocation de l’action de création sur la collection des users. De même, pour récupérer un utilisateur, GET /v1/users/007, se lit comme “Je veux l’utilisateur 007 dans cette collection d’utilisateurs”.

Casse consistante

Casse des URI

Lorsqu’il s’agit de nommer des ressources dans un programme informatique, on remarque majoritairement 3 types de style : CamelCase, snake_case et spinal-case. Il s’agit de nommer des ressources de manière proche du langage naturel en évitant toutefois d’utiliser des espaces, des apostrophes ou des caractères jugés trop exotiques. Cette pratique s’est imposée dans les langages de programmation où un ensemble réduit de caractères est autorisé pour identifier les variables.

CamelCase : Cette méthode a été démocratisée par le langage Java. Il s’agit de démarquer le début de chaque mot en mettant la première lettre en majuscule. Par ex. CamelCase, CurrentUser, AddAttributeToGroup, etc. En dehors des débats d’experts sur la lisibilité, son principal défaut est d’être inutilisable dans les contextes insensibles à la casse. Il existe deux variantes :
lowerCamelCase : où la première lettre est en minuscule.
UpperCamelCase : où la première lettre est en majuscule.
snake_case : Cette méthode est celle utilisée depuis longtemps en C et plus récemment en Ruby. Les mots sont alors séparés par des underscore « _ » permettant à un compilateur ou un interprêteur de le comprendre en tant que symbole unique, mais permettant au lecteur humain de séparer les mots de manière quasi naturelle. Sa baisse de popularité est en partie due aux abus des programmes en C, utilisant soit des noms à rallonge soit des noms ultra abrégés. À l’inverse du camel case, il existe très peu de contextes dans lesquels il est incompatible. Quelques exemples : snake_case, current_user, add_attribute_to_group, etc.
spinal-case : Variante du snake case, le spinal case utilise des tirets courts « – » pour séparer les mots. Les avantages et inconvénients sont en grandes partie identiques à snake case, à l’exception que de nombreux langages de programmation ne peuvent l’accepter en tant que symbole (nom de variable ou fonction). Il est parfois appelé lisp-case car en dialectes LISP, c’est la manière habituelle de nommer les variables et les fonctions. C’est aussi traditionnellement la manière dont les dossiers et les fichiers sont nommés dans les systèmes UNIX et Linux. Exemples : spinal-case, current-user, add-attribute-to-group, etc.

Ces trois types de nommages ont chacun leurs propres variantes en fonction d’autres critères comme la casse utilisée pour la première lettre ou le traitement réservé aux accents et autres caractères spéciaux. De manière générale, on préfère de toute façon utiliser l’anglais basique, exempt de caractères spéciaux.

D’après la RFC3986, les url sont “case sensitive” (hormis le scheme et le host). Mais en pratique, une casse sensitive peut entraîner des dysfonctionnements pour les API hébergées sous Windows.

Chez les Géants du Web :

Google	Facebook	Twitter	Paypal	Amazon	dropbox	github
snake_case	x	x	x			x	x
spinal-case	x			x			
camelCase	x

Nous recommandons d’utiliser pour les URI une casse consistante, à choisir entre :

spinal-case (mise en avant par la RFC3986)
et snake_case (fréquemment utilisée par les Géants du Web)
➡ Exemples

1
POST /v1/specific-orders
ou

1
POST /v1/specific_orders

Casse du body

Pour les données contenues dans le body, deux formats sont majoritairement utilisés.

La notation snake_case est sensiblement plus utilisée par les Géants du Web et notament adoptée par les spécifications OAuth2. En revanche, la popularité croissante du langage Javascript contribue significativement à l’adoption de la notation camelCase, même si sur le papier, REST devrait prôner l’indépendance du language et permettre d’exposer une API à l’état de l’art sur Xml.

Nous recommandons d’utiliser pour les URI une casse consistante, à choisir entre :

snake_case (fréquemment utilisée la communauté Web Ruby…)
lowerCamlCase (fréquemment utilisée par les communautés Javascript, Java…)
➡ Exemples

1
2
GET  /orders?id_client=007         or  GET /orders?idClient=007
POST /orders {"id_client":"007"}   or  POST/orders {"idClient":"007”}
Versioning

Toute API sera amenée a évoluer dans le temps. Une API peut être versionée de différentes manières:

Par timestamp, par numero de version,…
Dans le path, au début ou à la fin de l’URI
En paramètre de la request
Dans un Header HTTP
Avec un versioning facultatif ou obligatoire
➡ Chez les Géants du Web

API	Versioning
Google	URI path or parameter
https://www.googleapis.com/oauth2/v1/tokeninfo
https://www.googleapis.com/calendar/v3/
https://www.googleapis.com/drive/v2
https://maps.googleapis.com/maps/api/js?v=3.exp
https://www.googleapis.com/plus/v1/
https://www.googleapis.com/youtube/v3/
Facebook	URI (optional)
https://graph.facebook.com/v2.0/{achievement-id}
https://graph.facebook.com/v2.0/{comment-id}
Twitter	https://api.twitter.com/1.1/statuses/show.json
https://stream.twitter.com/1.1/statuses/sample.json
GitHub	Accept Header (optional)
Accept: application/vnd.github.v3+json
Dropbox	URI
https://www.dropbox.com/1/oauth2/authorize
https://api.dropbox.com/1/account/info
https://api-content.dropbox.com/1/files/auto
https://api-notify.dropbox.com/1/longpoll_delta
https://api-content.dropbox.com/1/thumbnails/auto
Instagram	URI
https://api.instagram.com/v1/media/popular
Foursquare	URI
https://api.foursquare.com/v2/venues/40a55d80f964a52020f31ee3
LinkedIn	URI
http://api.linkedin.com/v1/people/
Netflix	URI, optionel parameter
http://api.netflix.com/catalog/titles/series/70023522?v=1.5
Paypal	URI
https://api.sandbox.paypal.com/v1/payments/payment

Nous préconisons de faire figurer un numéro de version obligatoire, sur un digit, au plus haut niveau du path de l’uri.

Le numéro désigne une version majeure de l’API pour une ressource donnée, qui impose un changement pour que l’API fonctionne
REST et Json permettent, entre autre, par rapport à SOAP/xml, une certaine flexibilité pour faire évoluer l’API sans avoir à impacter (redéployer) les clients. Par exemple en ajoutant des attributs à une ressource existante. La version de l’API n’a pas à être incrémentée dans ce cas là.
Le versionning par défaut est à proscrire car en cas de changement de l’API, les impacts sur les applications appelantes seraient non maîtrisés par les clients.
La version d’une API étant une information essentielle, nous privilégions, pour des problématiques d’affordance, de le faire apparaître dans l’URL plutôt que dans le header HTTP.
Nous préconisons de supporter au plus, deux versions en parallèle (le cycle d’adoption par les applications natives est souvent plus long).
➡ Exemple

1
GET /v1/orders

CRUD

Comme nous l’avons indiqué, un des objectifs fondamentaux de l’approche REST est précisément d’utiliser HTTP comme protocole applicatif pour ne pas avoir à façonner une API « maison ».

Il convient donc d’utiliser systématiquement les verbes HTTP pour décrire les actions réalisées sur les ressources, et de faciliter le travail du développeur pour les manipulations CRUD récurrentes. Le tableau suivant synthétise les bonnes pratiques généralement constatées :

Verbe HTTP	Correspondance CRUD	Collection : /orders	Instance : /orders/{id}
GET	READ	Read a list orders. 200 OK.	Read the detail of a single order. 200 OK.
POST	CREATE	Create a new order. 201 Created.	–
PUT	UPDATE/CREATE	–	Full Update. 200 OK.
Create a specific order. 201 Created.
PATCH	UPDATE	–	Partial Update. 200 OK.
DELETE	DELETE	–	Delete order. 200 OK.

Le verbe HTTP POST est utilisé pour créer une instance au sein d’une collection. L’identifiant de la ressource à créer ne doit pas être précisé :

1
2
3
4
5
6
7
8
CURL –X POST \
-H "Accept: application/json" \
-H "Content-Type: application/json" \
-d '{"state":"running","id_client":"007"}' \
https://api.fakecompany.com/v1/clients/007/orders
 
< 201 Created
< Location: https://api.fakecompany.com/orders/1234
Le code retour n’est pas 200 mais 201.
L’URI et l’identifiant de la nouvelle ressource sont retournés dans le header “Location” de la réponse.

Si l’identifiant de la ressource est spécifié par le client, le verbe HTTP PUT est utilisé pour la création d’une instance de la collection. Cependant, en pratique, ce cas d’utilisation est moins fréquent.

CURL –X PUT \
-H "Content-Type: application/json" \
-d '{"state":"running","id_client":"007"}' \
https://api.fakecompany.com/v1/clients/007/orders/1234
< 201 Created
Le verbe HTTP PUT est en utilisé systématiquement pour réaliser une mise à jour totale d’une instance de la collection (tous les attributs sont remplacés et ceux qui sont non présents seront supprimés).

Dans l’exemple ci-dessous, on met à jour l’attribut state et l’attribut id_client. Tous les autres champs seront supprimés.

1
2
3
4
5
6
CURL –X PUT \
-H "Content-Type: application/json" \
-d '{"state":"paid","id_client":"007"}' \
https://api.fakecompany.com/v1/clients/007/orders/1234
 
< 200 OK
Le verbe HTTP PATCH (non présent initialement dans les spécifications HTTP mais ajouté ultérieurement) est couramment utilisé pour une mise à jour partielle d’une instance de la collection.

Dans l’exemple ci-dessous, on met à jour l’attribut state, mais les autres attributs restent tel quel.

1
2
3
4
5
6
CURL –X PATCH \
-H "Content-Type: application/json" \
-d '{"state":"paid"}' \
https://api.fakecompany.com/v1/clients/007/orders/1234
 
< 200 OK
Le verbe HTTP GET est utilisé pour lire une collection. En pratique, l’API ne retourne généralement pas l’intégralité des réponses (voir section Pagination).

1
2
3
4
5
6
CURL –X GET \
-H "Accept: application/json" \
https://api.fakecompany.com/v1/clients/007/orders
 
< 200 OK
< [{"id":"1234", "state":"paid"}, {"id":"5678", "state":"running"}]
Le verbe HTTP GET est utilisé pour lire l’instance d’une collection.

CURL –X GET \
-H "Accept: application/json" \
https://api.fakecompany.com/v1/clients/007/orders/1234
 
< 200 OK
< {"id":"1234", "state":"paid"}

## Réponses partielles

Les réponses partielles permettent au client de récupérer uniquement les informations dont il a besoin. Cette fonctionnalité est par ailleurs primordiale pour les contextes en utilisation nomade (3G-), ou la bande passante doit être optimisée.

➡ Chez les Géants du Web

API	Partial responses
Google	?fields=url,object(content,attachments/url)
Facebook	&fields=likes,checkins,products
LinkedIn	https://api.linkedin.com/v1/people/~:(id,first-name,last-name,industry)
A minima, nous préconisons de pouvoir sélectionner les attributs à remonter, sur un niveau de ressource, via la notation Google fields=attribute1,attributeN :

1
2
3
4
5
6
7
GET /clients/007?fields=firstname,name
200 OK
{
  "id":"007",
  "firstname":"James",
  "name":"Bond"
}
Pour les cas où les performances constituent un enjeu fort, nous proposons, d’utiliser la notation Google fields=object(attribute1,attributeN). Par exemple pour ne récupérer que le prénom, le nom et la rue de l’adresse d’un client :

1
2
3
4
5
6
7
8
GET /clients/007?fields=firstname,name,address(street)
200 OK
{
  "id":"007",
  "firstname":"James",
  "name":"Bond",
  "address":{"street":"Horsen Ferry Road"}
}

