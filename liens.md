# Liens web

* [Elegant APIs with JSON Schema](https://brandur.org/elegant-apis)
* [REST Metadata Formats](http://apiux.com/2013/04/09/rest-metadata-formats/)
* [REST Guide](http://scytl.github.io/restguide/)
* [API Managemetn selon WSO2](http://wso2.com/api-management/)
* [Blog API Evangelist](http://apievangelist.com/blog/)
* [Security JAX-RS 2.0](http://bitsuppliers.com/securing-rest-resources-with-java-ee-7-and-jax-rs-2-0/)
* [Astuce Apache Camel](http://www.javacodegeeks.com/2013/01/apache-camel-cheatsheet.html)
* [Apache Camel : Binding Style CXF cf EME pour custom](http://camel.apache.org/cxfrs.html#CXFRS-ConsumingaRESTRequest-SimpleBindingStyle)
* [Apache Camel : Binding Style SimpleConsumer & Default](http://www.rubix.nl/blogs/exploring-simpleconsumer-and-default-camel-cxfrs-binding-styles)
* [Apache Camel : Comment setter une propertie du body dans une route](http://stackoverflow.com/questions/17708962/set-property-on-body-using-bean)

I've been complaining on camel-users that there isn't a good way to do this. For now here is how I tackle it:

.setHeader("dummy").ognl("request.body.articleId = request.headers.articleId")
Which requires adding camel-ognl dependency.

UPDATE

Actually, there is also a language endpoint that can do this without the setHeader, but you have to say transform=false or else it replaces your body with the result:

.to("language:ognl:request.body.articleId = request.headers.articleId?transform-false")

# Autres

* [JPA create & update date](http://blog.octo.com/en/audit-with-jpa-creation-and-update-date/)
* [Validation JAX-RS en JEE7 dans wildfly](http://samaxes.com/2014/04/jaxrs-beanvalidation-javaee7-wildfly/)

# Domotique

* [Experiance déploiement Asterisk](http://people.via.ecp.fr/~alexis/asterisk/)
* [1-wire](https://madomotique.wordpress.com/2011/10/18/le-protocole-1-wire/)
* [blog bien expliquer niveau techinque](https://www.domolio.fr/page/2/)
* [Comment choisir ces cables RJ45](http://blog.casanova-life.com/2008/10/10/les-performances-des-reseaux-de-communication-rj45/)
* [Blog d'un mec de chez IBM : node-red et watson](http://g00glen00b.be/creating-a-slack-bot-with-bluemix-node-red-and-watson/)

# Sécurité

* [podcast sécu](http://www.nolimitsecu.fr/)
* 
