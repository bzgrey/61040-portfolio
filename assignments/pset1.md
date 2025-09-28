# Assignment 2 (6.104) - Benjamin Grey

## Exercise 1: Reading a Concept
1. **Invariants**: One invariant is that for each request, the aggregate sum of the counts of all of the purchases of the item of that request is less than or equal to the count of that request. The second invariant is that no purchase is made in a registry for an item that doesn't exist in that registry as a request. The second is more important becuase a person doesn't want to get something that they didn't request, but if they got a larger number of items than they requested they could be more likely to have a use for it. The purchase action is most affected by it and it preserves this invariant by first checking that the item being purchased exists as a request in the chosen registry, and that there are enough left that are requested and haven't been purchased.

2. The removeItem action can break this invariant because it could remove an item that was already selected for being purchased, so there will then exist purchases for items that don't exist in the selected registries. This problem might be fixed by requiring as a precondition that the Item not have already been purchased by somebody in that registry.

3. According to the specs, a registry can be opened and closed repeatedly, because the open action has no precondition that the registry was not previously closed. It just checks whether it is active, and the close action just changes the active flag to not active. A reason to allow this is because a person may change their mind after closing the registry and want to reopen it.

4. This could matter if a wedding was called off. Maybe the previous couple wouldn't want the registry to be out there even if it was closed. Maybe closing it could also close it from being viewable to the public in which case that wouldn't be a problem.

5. A registry owner would likely query for each of his requests, what purchases have been made and by whom. A gift giver would query for a registry what requests that person made and what is still available to buy.

6. I would remove the last line of the concept principle that "the recipient can see which items were purchased and by whom."

7. This is because SKU codes are unique identifiers for different items, and they are easily queriable in a database while keeping the code simple in terms of just using SKU codes to represent items.

## Exercise 2: Extending a familiar concept
1. **State** \
    A set of **Users** with 
    - a username String
    - a password String
2. register (username: String, password: String): (user: User) \
**requires** a User with username doesn't already exist in the set of Users \
**effects** a User with username and password is added to the set of Users\
authenticate (username: String, password: String): (user: User)\
**requires** a User with username and password exists in the set of Users
**effects**

3. No two Users have the same username must be an invariant. The register action holds this invariant by checking if there already exists a User with the same username.

4. 
**State** \
...\
   A set of Tokens with \
      a token String \
      a username String \
...\
**Actions**\
   register (username: String, password: String): (user: User, token: String) \
...\
...\
   confirm(username: String, token: String): (user: User) \
      **requires** \
      a token exists in the in the set of Tokens with the given username and token strings\
      **effects** \
      the token in Tokens with the token and username strings is removed from the set of Tokens

## Exercise 3: Comparing Concepts
**concept** PersonalAccessToken [User]  
**purpose** provide users with multiple ways of gaining limited access
**principle** a previously known user can generate multiple personal access tokens. After generating the tokens, they can be used by the user as alternative (to passwords) ways to authenticate into the user's account from different interfaces. Each token can be given an expiration, and it can be deleted by the user while the user still exists

**state**   
a set of Tokens with  
  a token String  
  a user User  
  an expiration Date 

**actions**  
  create(user: User, expiration: Date): token: Token     
    **effects**     
      creates a new token for this user, with given   expiration date   

  authenticate(token: String): user: User  n  
    **requires**   
      token string exists in set of Tokens
  
  delete(user: User, token: Token): token: Token  
    **requires**    
      Token exists in the set of Tokens with given user  
    **effects**  
      removes Token with the given token string

This is different than PasswordAuthentication in a number of ways. The token is generated for a user that already exists and can authenticate with a password, and a user can create multiple tokens for themself, while a user can only have one password. Additionally, the token has an expiration, it can be deleted, and it is usually used to provide easy access in different situations than when authenticating on a website, like in the command line for example.       
I think I would make more clear in the Github documentation the differences between  the token and a password, and why it is used. It should make describe in more detail the different use cases and when a person would want to use the tokens in the command line, and how one can have multiple tokens and delete them or have them expire.

## Exercise 4: Defining Familiar Concepts

### URL Shortener
**concept** URLShortener  
**purpose** shorten and simplify long urls  
**principle** someone who wants to share a long, complicated url with others can input it into a URLShortener and either provide a user-defined or autogenerated suffix to a shared prefix url. This url, when searched, redirects to the original url  

**state**  
a urlPrefix String  

a set of URLPairs with  
   an old url String  
   a new suffix String

**actions**     
  createCustom(old url: String, suffix: String): URLPair  
    **requires**    
      a URLPair with the given suffix doesn't already     exist   
    **effects**  
      adds a URLPair with the given old url and new   suffix to the set of URLPairs     

  generateURL(old url: string): URLPair     
    **effects**  
  adds a URLPair with the given old url and new computer generated suffix (that doesn't already exist) to the set of URLPairs     

Notes: This is made so anyone can easily make a url, without having to sign in or anything or worry about the url, allowing for both custom and computer generated suffix's to be created.

### Conference Room Booking
**concept** RoomBooking [User]  
**purpose** easily book rooms for events  
**principle** An organization that has a building can make rooms in its building(s) available, and people can then reserve those rooms at specific times for events, meetings, and the like such that others cannot then reserve those rooms    
**state**   
  a set of Rooms with   
    a room Number   
    a set of Slots with     
      a Time    
      a reserved Flag   
      a User (if reserved)

**actions**     
  reserve(room: Room, time: Time, user: User): room: Room   
    **requires**  
      room is not reserved at given time(s) - reserved flag = False  
    **effects**  
      marks the Slots of given room at given time(s) as reserved (reserved Flag = True), and sets the user who reserved it as given user   
  
  cancel(room: Room, time: Time, user: User)
    **requires**  
      room is reserved at given time(s) by given user - reserved Flag = True   
    **effects**  
      marks the Slots of given room at given time(s) as not reserved (reserved Flag = False), and removes the user who reserved it

Notes: The time that a user inputs is a range of times, and then the corresponding slots are selected.

### Time-Based One-Time Password (TOTP)

**concept** TOTP [User]   
**purpose** more securely authenticate into account various accounts with extra password    
**principle** an organization can add TOTP to their authentication system, by addoing a redirect after the input of a password to a place to input the TOTP. The TOTP can be found on a separate app on the user's phone, and when inputed, the user is fully authenticated. The password is regenerated frequently for security purposes.   
**state**  
  a set of Organizations with   
    an organization id String   
    a set of Users  
  a set of Users with   
    a temporary password String     

**actions**
  join(organization: Organization): Organization
    **requires**    
      organization doesn't already exist    
    **effects**     
      adds organization to set of organizations 

  removeOrganization(organization: Organization): Organization  
    **requires**      
      organization already exists   
    **effects**     
      removes organization from set of organizations   

  addUser(organization: Organization, user: User)   
    **requires**    
      organization already exists and user doesn't exist in the set of users for that organization, but the user does exist in the general set of users  
    **effects**  
      adds user to that organization's set of users  

      removeUser(organization: Organization, user: User)   
    **requires**       
      organization already exists and user exists in the set of users for that organization  
    **effects**  
      removes user from that organization's set of users

  authenticate(organization: Organization, one-timePassword: String): User  
    **requires**        
      There exists presently a user with the given one-timePassword who also exists in the organization's set of users  
    **effects**     
      user's one-timePassword is regenerated to a new random simple code   

Notes: the one-timePassword is also regenerated frequently to a new random string, which is usually a simple, short code of numbers. This provides extra security because of multi-factor authentication - a predator needs to get passed two barriers. The possible problem could be if that predator figured out the password to the authentication app (if he used the same passwod), then it wouldn't help. 