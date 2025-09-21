# PSet 2: Composing Concepts

## Concept Questions

1. The reason for contexts is that you may want to reuse certain nonce strings in different but similar contexts. Here specifically, the context is a baseURL, so multiple baseURL's would be able to use the same suffix without a problem.

2. NonceGeneration stores used strings so that when new nonces are generated, they will be different than all of the used ones. In this implementation, each count must represent some nonce such that the set of used strings is just all of the mapped strings from 0 to the current count.
3. An advantage of this is that the new url is short and easily memorable for it to be shared easily. A disadvantage is that it may be a word that would misrepresent what the website it links to actually does.

## Synchronization Questions
1. This is because the first sync is a more general sync that occurs no matter what the targetURL ends up being or if it was made at all yet. It is just passing context (the baseURL) to create a nonce suffix that can be used later. The rlShortening.register action hasn't actually been called yet. In the 2nd sync, the goal is to call the register action, so for that, a target url is also needed.
2. This isn't used in every case because the variable name is often more general than the specific argument or return name. For example, in the setExpiry sync, the shortURL is a specific type of resource, but it doesn't encompass every type of resource. 
3. This is because the first two syncs occur specifically as a result of user "request", whereas the third only happens when a specific concept action occurs, regardless of what led to the action happening.
4. I would modify the generate sync such that there are no arguments to Request.shortenUrl and the context input to NonceGeneration.generate is just the specific fixed domain. I would modify the register sync such that Request.shortenUrl only has targetURL as an argument and specify the input to NonceGeneration.generate for the base to be the specific fixed domain.
5. **sync** expire    
**when** ExpiringResource.expireResource(): (resource: shortURL)    
**then** UrlShortening.delete(shortUrl)


## Extending the Design
1. 

**concept** LinkAuthorization[User]  
**purpose** authenticate owners of links     
**principle** if someone wants to protect the access of information in relation to a url to the owner of the url, they can protect it, so only people who authorize who are the owner of the requested link can access it  
**state**   
  a set of Links with     
    a url String    
    a link owner User   
**actions**     
  add(url: String, owner: User): User   
    **requires**  
      url doesn't exist in set of Links     
    **effects**     
      adds new url with its owner to set of Links   
  authenticate(url: String, owner: User): User     
    **requires**    
      owner is an owner of the given url    

**concept** Analytics   
**purpose** track analytics of accesses of urls  
**principle** someone with a link can track the analytics of it, specifically the number of times it is accessed so that whenever someone accesses the link, the count is updated, and the owner of the link can then see these analytics   
**state**   
  a set of Links with   
    a url String    
    an accessCount Number     
**actions**     
  track(url: String): Link   
    **requires**  
      url doesn't exist in set of Links     
    **effects**  
      adds Link to set of Links with accessCount of 0
  access(url: String)    
    **requires**  
      url exists in set of Links 
    **effects**     
      adds one to the accessCount of given url
  get(url: String): Number  
    **requires**  
      url exists 
    **effects**     
      returns accessCount of given url 

2. 
**sync** newAnalytics   
**when**   
 Request.register(owner: User)  
 UrlShortening.register(): (shortURL)  
**then**  
  LinkAuthorization.add(owner, shortURL)
  Analytics.track(url: shortURL)  

**sync** accessURL     
**when** UrlShortening.lookup(shortURL): (targetURL)    
**then** Analytics.access(shortURL)      

**sync** getAnalytics   
**when**  
  Request.get(owner, shortURL)   
  LinkAuthorization.authenticate(owner, shortURL)   
**then** Analytics.get(shortURL)       

3.
-  Allowing users to choose their own short URLs: 
    - I would add a sync called RegisterCustom that takes in a custom suffix as an input to the Request, and uses that as the suffix in the UrlShortening.register action
- Using the “word as nonce” strategy to generate more memorable short URLs
    - In the NonceGeneration concept, in the state I would include a large set of memorable words such that the generate action picks one of those words that hasn't been already picked for the given concept. Additionally, for each context, I would add flag "wordAsNonce" to determine whether the memorable word bank should be used for nonce generation or not for that context.
- Including the target URL in analytics, so that lookups of different short URLs can be grouped together when they refer to the same target URL;
    - All that would need to be changed is that in the newAnalytics sync, Analytics.track should be called on the targetURL as well (if it is anot already being tracked), and in the accessURL sync, Analytics.access should also be called on the targetURL. Also the targetURL needs to be added to the authorization concept.
- Generate short URLs that are not easily guessed;
    - Have the generate action in NonceGeneration generate random short strings if the "wordAsNonce" flag from above is flagged as false.
- Supporting reporting of analytics to creators of short URLs who have not registered as user.
    - I think that this is undesirable because these short url sites require a user to track the urls, and also it would be hard to maintain and for non-users to be able to go back to their analytics without any way of signing on, as it would be undesirable to make the analytics data public.

