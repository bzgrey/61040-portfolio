# Concept Design
**concept** Label[Item]     
**purpose** track the items that are associated with different labels       
**principle** When someone wants to know what items are related to certain topics or labels, they can create a new label and then add items that are associated to that label. They are also able to remove items or labels if they change their minds.     
**state**   
  a set of Labels with  
     a label String  
     a set of Items  
**actions**     
  addLabel(label: String): Label  
    **requires**  
      Label does not already exist with the given label string  
    **effects**  
      adds new label to set of Labels with the given label string   
  removeLabel(label: String): Label  
    **requires**  
      Label exists with the given label string  
    **effects**  
      removes the label and its associated items from the set of Labels     
  addItem(label: String, item: Item): Item  
      **requires**  
        Label exists with the given label string and item does not already exist in the label  
      **effects**  
        adds new item to the set of Items associated with the given label  
    removeItem(label: String, item: Item): Item  
      **requires**  
        Label exists with the given label string and item exists in the label  
      **effects**  
        removes item from the set of Items associated with the given label  
  get(label: Label): set of Items  
    **requires**  
      Label exists with the given label  
    **effects**  
      Returns the set of Items associated with the given label  


**concept** Following[User, Item]   
**purpose** track items of interest         
**principle** a User selects items to follow and then the user can access them and can also unfollow them  
**state**   
  a set of Users with   
    a set of followed Items  
**actions**  
  follow(user: User, item: Item)    
    **requires**  
      If user exists, the user is not already following the item  
    **effects**  
      If the user doesn't exist, adds user to set of users. Adds the item to the set of followed Items for the given user  
  unfollow(user: User, item: Item)  
    **requires**  
      User exists and is currently following the item  
    **effects**  
      Removes the item from the set of followed Items for the given user  
  getFollowedItems(user: User): set of Items  
    **requires**  
      User exists  
    **effects**  
      Returns the set of Items currently followed by the given user  

**concept** FlashCards[User, Label]    
**purpose** create easy way to review topic of choice with questions and answers     
**principle** a user can create flashcards on different topics and can add or remove specific cards with questions and answers on them for any flashcards topic. the user can also associate labels with any set of flashcards     
**state**   
  a set of FlashCards with  
    a User  
    a name String   
    a set of Cards with     
      a question String     
      an answer String  
    a set of Labels  
  a set of Users with  
    a set of FlashCards     
**actions**     
  addFlashcards(user: User, name: String, cards: Cards, labels: Labels): FlashCards   
    **requires**    
      FlashCards don't already exist with the same user and name    
    **effects**     
      adds new flashcards to set of FlashCards associated with the given user, name, labels, and cards. Also adds new flashcards to set of Users    
  removeFlashCards(user: User, name: String): FlashCards    
    **requires**    
      FlashCards exist with the same user and name    
    **effects**     
      removes flashcards with given name and user from both FlashCards set and given user's set     

  addCard(user: User, name: String, question: String, answer: String): Card   
    **requires**    
      FlashCards already exist with the same user and name    
    **effects**     
      adds new card to FlashCards of given name and user with given question and answer     

  removeCard(user: User, name: String, card: Card): Card   
    **requires**    
      FlashCards already exist with the same user and name and the given card exists in those FlashCards      
    **effects**     
      removes card from FlashCards of given name and user   

  addLabel(user: User, name: String, label: Label): Label   
    **requires**    
      FlashCards already exist with the same user and name    
    **effects**     
      adds new label to FlashCards of given name and user   

  removeLabel(user: User, name: String, label: Label): Label   
    **requires**    
      FlashCards already exist with the same user and name and the given label exists in those FlashCards      
    **effects**     
      removes label from FlashCards of given name and user  

**concept** Notes[User, Label]   
**purpose** store notes on different topics or classes with labels    
**principle** a user can store notes and give a label to them; the user can also remove notes of labels   
**state**   
  a set of Notes with   
    a User  
    a name String   
    a notes String  
    a set of Labels     
  a set of Users with   
    a set of Notes  

**actions**     
  addNotes(user: User, name: String, notes: String, labels: Labels): Notes   
    **requires**    
      Notes don't already exist with the same user and name    
    **effects**     
      adds new notes to set of Notes associated with the given user, name and labels. Also adds new Notes to set of Users    
  removeNotes(user: User, name: String): Notes    
    **requires**    
      Notes exist with the same user and name    
    **effects**     
      removes notes with given name and user from both Notes set and given user's set     

  addLabel(user: User, name: String, label: Label): Label   
    **requires**    
      Notes already exist with the same user and name    
    **effects**     
      adds new label to Notes of given name and user   

  removeLabel(user: User, name: String, label: Label): Label   
    **requires**    
      Notes already exist with the same user and name and the given label exists in those Notes      
    **effects**     
      removes label from Notes of given name and user    
  

### Syncs  
**sync** addCardsLabel   
**when** Request.labelCards(user: User, label: Label, flashcards: FlashCards, name: String)    
**then**    
  Label.addLabel(label): Label  
  Label.addItem(label, item: flashcards)    
  FlashCards.addLabel(user: User, name: String, label: Label)

**sync** addNotesLabel  
**when** Request.labelNotes(user: User, label: Label, notes: Notes, name: String)  
**then**  
  Label.addLabel(label): Label  
  Label.addItem(label, item: notes)  
  Notes.addLabel(user: User, name: String, label: Label)

**sync** removeCardsLabel  
**when** Request.unlabelCards(user: User, label: Label, flashcards: FlashCards, name: String)  
**then**  
  Label.removeItem(label, item: flashcards)  
  FlashCards.removeLabel(user: User, name: String, label: Label)

**sync** removeNotesLabel  
**when** Request.unlabelNotes(user: User, label: Label, notes: Notes, name: String)  
**then**  
  Label.removeItem(label, item: notes)  
  Notes.removeLabel(user: User, name: String, label: Label)
### Note on Concept Roles and Type Parameters

The concepts above work together to support a flexible study and organization app. The `Label[Item]` concept provides a generic way to associate any kind of item (such as flashcards or notes) with topic labels, enabling users to categorize and retrieve related content easily. The generic type parameter `Item` is instantiated as either a `FlashCards` or `Notes` object, depending on context.

The `Following[User, Item]` concept allows users to track items of interest, where `User` is bound to the app's user accounts and `Item` is typically instantiated as either `FlashCards` or `Notes`. This enables personalized content tracking.

The `FlashCards[User, Label]` and `Notes[User, Label]` concepts both use `User` for ownership and `Label` for categorization. Flashcards and notes can be labeled for organization, and both support adding/removing labels, which syncs with the `Label` concept to keep associations consistent.

Syncs ensure that when labels are added or removed from flashcards or notes, the `Label` concept is updated accordingly, maintaining bidirectional consistency. Overall, generic type parameters are instantiated in the most natural way: `User` for users, `Label` for topic labels, and `Item` for either flashcards or notes, depending on the operation.
