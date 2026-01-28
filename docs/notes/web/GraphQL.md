

so it basiclly a query language to replace RESTfull api
the `query` is like GET request in RESTfull api so it retrive data only 
and `mutation` is wrting data to like POST

-  see if **Introspection** is enable so [you can retrive data about the schema](https://portswigger.net/web-security/graphql#running-a-full-introspection-query):

 ```graphQL
 { "query": "{__schema{queryType{name}}}" }
 ```

Mybe it is disable you can try this tool: [clairvoyance](https://github.com/nikitastupin/clairvoyance) 

- Suggestions are a feature of the Apollo GraphQL platform. 
> "There is no entry for 'productInfo'. Did you mean 'productInformation' instead?)."

you can use [graphql visualizer](http://nathanrandal.com/graphql-visualizer/) or Burpsuit (GraphQL -> send to sitemap), inQl 

