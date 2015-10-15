Liens web

* [Elegant APIs with JSON Schema](https://brandur.org/elegant-apis)
* [REST Metadata Formats](http://apiux.com/2013/04/09/rest-metadata-formats/)
* [API Managemetn selon WSO2](http://wso2.com/api-management/)
* [Blog API Evangelist](http://apievangelist.com/blog/)
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


