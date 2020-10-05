# journal
journal for jessie

- [Data Model](https://docs.google.com/spreadsheets/d/1Z7QqBW91Jye4lVplfZYxUCWf3E1UgJoHvRGOWgsBwdU/edit?usp=sharing)
- [Code](https://github.com/tarrie/io.tarrie.api)
    - [Current Branch in this pull req](https://github.com/tarrie/io.tarrie.api/pull/1)

# 12/14/19

- Created a AWS account, the domain tarrie.io is available via AWS Route 53 thank the lord! However its $40 bucks should I pay for it? 
- Converted RAML to Swagger very easy to do. The API I created before is close to complete but missing details on authentication so I'll spend my time filling those in- here comes AWS Cognito!
- AWS Cognito has JWT tokens which is what I was looking for,however,  I want to add some claims to the payload of the token so I'm trying to figure that out. 
    - Found how to add claims to token -> https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-lambda-pre-token-generation.html , needs lambda or access to AWS CLI. Lambda will get expensive so just use AWS CLI have to verify that I can access CLI from a EC2 Instance (aka BeanStalk).
        - Turns out I can: https://stackoverflow.com/questions/45844779/is-it-possible-to-execute-aws-cli-commands-on-an-ec2-instance-without-placing-aw
        - So lets just develop on local machine using AWS CLI then set up the IAM roles later. 
- To start lets create a api endpoint for groups -- becoming admin, owner, club member, subscribe -- most if not all need jwt access. 
    - Took a while to get the tomcat server env up and running but I got a hello world example up. Now lets implement the api schema for groups
    - Actually now lets set up swagger integration w/ jersey and then we can be done for the day
    - Finished. so got the java server env set up so things should be fast. btw mvn is surpsingly annoying. found out how to define datamodels for swagger to! use this link tommorow now the api generation will be semantic https://www.vojtechruzicka.com/documenting-spring-boot-rest-api-swagger-springfox/
        - TOMMORROW: GET api for group membership going, use AMAZON CLI TO add group membership to the payload of jwt token. honestly not sure this matter that much but i guess if people really want their groups to be secure. whatever. 
       
# 12/15/19
- Found good documentation for making API model 
    - https://jakubstas.com/spring-jersey-swagger-create-documentation/#.XfagyNZKjBI
    - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-general-nosql-design.html
    - https://www.vojtechruzicka.com/documenting-spring-boot-rest-api-swagger-springfox/
    - https://medium.com/shark-bytes/co-hosting-swagger-ui-with-your-jersey-rest-api-using-maven-dependencies-44d88ae85bf8
- Only problem is that the response should be a json. So i want to convert a java class to json. Found it: https://blog.codota.com/how-to-convert-a-java-object-into-a-json-string/
- So first get the model down, then keep trying to make the groups endpoint then check out cognito. 
- So im close to finishing the model, however there are certain things i wont model for now like the contact list. I just wanted to get one of the groups endpoints out, but everything is so interrelated i had to create objects for events, eventtime, users, location, membership. So this is taking some time. My goal is still to get a MVP -- the groups endpoint -- so i can figure out congnito but I'm getting sidetracked. 
- https://portswigger.net/web-security/csrf/tokens After i figure out JWT lets figure out csrf.. or maybe not. .. lets just keep this in mind
- New goal just get the api working without worrying about jwt(obvious yes). Why? Well its not that straightforward to use the swagger annotations and to consume json payloads so i need to figure this out. As a benefit at least the mvp api will be produced w/ corresponding documentation. 
    - Main reference url:  https://www.mkyong.com/tutorials/jax-rs-tutorials/
    - https://github.com/swagger-api/swagger-core/wiki/Swagger-2.X---Getting-started
- OK so if i use this -> https://github.com/swagger-api/swagger-core/wiki/Swagger-2.X---Getting-started like the poml depedencies the whole thing throws errors. Took a couple hrs to figure out. I implemented the jersey-jackson implementation listed here --> https://www.mkyong.com/webservices/jax-rs/json-example-with-jersey-jackson/ for a basic endpoint that does nothing because ingesting json payload is not close to straight forward. After making everything public in my pojo object for the payload it started woring. I also made a python client since it only took 3 lines of code and java would basically take 1-million, I see why people like python now. I'm stuck because the swagger-core documentation is missing something. Have to fix this or I wasted most of the day since my annotations aren't showing up in the api documenation

# 12/16/19
- Added a class to convert POJO to json for API, still working on groups API but i want the endpoint to post to dynamboDB so im working on this now. How to orangize my DB, structure it, access it
- Setting up DynamoDb with Java https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.Java.html
    - The free tier comes with alot of data!!
- https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/
- https://aws.amazon.com/blogs/database/using-sort-keys-to-organize-data-in-amazon-dynamodb/
- The first one seems nice, good amt of work to do to set DynamoDb up and understand it. I created a Admin Secret Access Keys, and had to set up new permisions. Close but no cigar lets do it tommorrow. Also need to figure out how to define the schema aka how to structure dynamodb. Store the schema in the ressource folder? idk. Right now i think evrything looks pretty organized. gitignore did not ignore my access keys so got secuirty risk emails from AWS oh well, crazy how fast they found my github and the access keys though. sheehs!
    - https://github.com/tarrie/io.tarrie.api/pull/1 -- contains the brANCH that i'll be working with. 
    
# 12/17/19
- https://www.dynamodbguide.com/key-concepts/
- https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-general-nosql-design.html
#### DynamoDb
- ***composite key***: specify both a partition key and a sort key.
    -  When setting an attribute for a DynamoDB item, you must specify the type of the attribute. Available types include simple types like strings and numbers as well as composite types like lists, maps, or sets.
    - Main ref: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.Java.html
    - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/CodeSamples.Java.html
    - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/AppendixSampleDataCodeJava.html
    - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/JavaDocumentAPITablesExample.html
    - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-relational-modeling.html
# 12/18/19
following https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-modeling-nosql.html
Shit is kinda confusing im going to watch the AWS playlist- https://www.youtube.com/watch?v=W3S1OnDqWl4 and just document what the fuck i've found
## DynamoDb
- WCU -1KB in size. If writing item that is 1kb or less than each write will consume 1 write capacity unit
- RCU -4kb in size. Reading 4kb will consume
### Sort Key
- Allows to model 1:N relationship. So a camera has a deviceID, and this device produces N pictures. The sort key allows us to model this. 
### Local Secondary Index
### Global Secondary Index
- Create a group
    -  Add user to a group
- Create a event
    - tag the event with relevant hashtags
    - Invite user to a event
- Create a user
- Share event
- Change status of group member. 
- rsvp to a event
- get a list of available events. 
- filter event by hash tag

Thoughts: eventId is PK, date is SK. So sort by date

# 12/19/19
- https://www.trek10.com/blog/dynamodb-single-table-relational-modeling/
    - AWS Auro is SQL and distributed vs DynamoDB
        - DynamoDb- single-table data model is both informal and inflexible. It’s not necessarily going to accommodate new access patterns.
        - SQL - flexible access pattern w/ joins and what not. 
- https://www.jeremydaly.com/takeaways-from-dynamodb-deep-dive-advanced-design-patterns-dat403/
- Rick's DynamoDB Advanced Design Patterns talk - https://www.youtube.com/watch?v=6yqfmXiZTlM
- https://www.jeremydaly.com/how-to-switch-from-rdbms-to-dynamodb-in-20-easy-steps/

***Point 1:*** Basically NoSQL design is creating a relational model (ER-Diagram) then dumming it down to reflect the access patterns of the application. So NoSQL design is optimized for cookie-cutter /create/delete/update/get (CRUD) operations that are commonly called, and in general key-value request which alot of apps seem to be, but if the application needs more adhoc queries noSql won't work as well because this would involve entire table scans. <br>
***Point 2:*** To figure out access patterns a easy way is to have the entire API schema in hand. This is kind of the same for relational models since you can create entities w/o a API but not really the linking tables or the foriegn keys without some sort of design. But NoSQL takes to the extreme so i think the entire api schema needs to at least be sketched out. <br>
***Conclusion*** Design the API by including stub methods with descriptions of what they will do. So i think this is a perfect use of JAVA Interfaces since they support annotations and allow me to declare stub methods. After defining the interface I'll have the access patterns and then i'll see if most of it is just key-value (aka NoSQL) or if I need more relations (aka SQL). <br>
- Groups & User get autogenerated email they can send shit to?
- Find out how I can respond to people from Linkden via email, and the linkden app is updated. Like how? They use this email inmail-hit-reply@linkedin.com and somehow it does the mapping. So like if Tom Recrutier messages me on linkden,  his name and message will get to my email under inmail-hit-reply@linkedin.com. If i respond to this email then if responds to Tom I'm guessing through the same proxy email. I understand how the system can know I sent it via my own actual email, but how do they know who im responding to? Probably some information that I'm missing that included in a email

- Websockets via Application Load Balancer
    - https://stackoverflow.com/questions/39336033/does-an-application-load-balancer-support-websockets
### From Grokking System Design: Messaging

- ***Functional Requirements***
    - Messenger should support one-on-one conversations between users.
    - Messenger should keep track of the online/offline statuses of its users.
    - Messenger should support the persistent storage of chat history.
    - Group Chats: Messenger should support multiple people talking to each other in a group.
- ***Non Functional Requirements***
    - Users should have real-time chat experience with minimum latency.
    - Our system should be highly consistent; users should be able to see the same chat history on all their devices.
    - Messenger’s high availability is desirable; we can tolerate lower availability in the interest of consistency.
- ***Extenede Requirements****
    - Push notifications: Messenger should be able to notify users of new messages when they are offline.
    
#### High Lvl Design
- User-A sends a message to User-B through the chat server.
- The server receives the message and sends an acknowledgment to User-A.
- The server stores the message in its database and sends the message to User-B.
- User-B receives the message and sends the acknowledgment to the server.
- The server notifies User-A that the message has been delivered successfully to User-B.

So in this design User-A is connect to it own server, and User-B is connected to its own server. All sending and recieving is between respective servers. 

Actually lets just skip chats for now. However whatevern we implment needs some sort of real time notification and thats basically the main issue for chats. <br>

Even the interface is taking hours to write, probably going to cancel the meeting downtown with the bro recruiter. 
- ***CRUD API design*** uses POST for Create and PUT for Update, which has to do with the semantics of these verbs:

# 12/20/19

*** New objects***
- Notifications
    - event invitations
    - contact request
    - group invitations
    - people posting shit in the event thread. 
    - other shit I like linkden's format for this
- Inbox
    - actual messages from other user
    - messages from events
- Event Thread
    - basically a thread that people can post shit to
- HashTags
    - ML Hierichal Clustering allow navigation for hashtags
    
Extra Feature
- How to deal with misspelled hashtags???
    - ElasticSearch has a fuzzy match paremter - https://www.elastic.co/blog/found-fuzzy-search
https://www.mkyong.com/webservices/jax-rs/jax-rs-queryparam-example/

Creating own chat sounds crazy lets use open source -https://stackoverflow.com/questions/10680563/what-is-a-good-open-source-chat-solution

# 12/22/19
- Complexity of the API is overwhelming
- Before I only had 3 membershipTypes: Owner, Admin, Subscriber. Now we have 4: Owner, Admin, ClubMember, Follower. 
	- So techincally there will be 3 subgroups in a group: Admins, ClubMembers, Followers
	- Add ability to make more subgroups that can be made of groups or users - like Like PhdStudents, MasterStudents
	    - Is there a way to integrate existing sub groups from email provider?
- Add ability for users to create subgroups as well. Initial subgroup will be empty? Purpose is to make it easy to send out
invites to events based on subgroups. Synonomouys to email blasting. 
- In general: Need a way to make subgroups out of users and name them so we can repeatedly give these users access or communicate with them. Like PhdStudents, MasterStudents
- Need to figure out how to add access to event in creation. I think the filter for now can be users who can see it 
    - Subgroups are based on users btw 
    - Is there a way to get existing groups from email provider?
- If you are a member of a group you are automattocally following it. If you leave a group you are not following it.
- Almost finished with defining the abstract interface. Pickup tommorow on the `User` interface. Needs to be cleaned up and add the events path. 
- Lots of logical errors in the model go over this as well.
- Users is under path `\users` and Groups is under path `groups`, before had all events under the path `events` but that wasnt clean. Swithced to `\users\{userId}\events` and `\groups\{groupId}\events` endpoint to manipulate specific shit. Needs cleaning up
***Nice to have features: Implement in next batch***
- Groups: Features that would be nice to have`
    - invite user to join a group via Name, email address, or handle -- similar to Github
    - Email blast members of the group
    - Message the members of the group
    - Chat with group clubMembers
- Event: Features that would be nice to have
    - Write on the events page? So events have a feed
    - shareEvent via email - Needs email service - and to a subgroup
    - shareEvent via Tarrie - Needs notification object and SNS
    - List related events to a given Hashtag - Needs ML + clustering
    - Support for multiple images when creating a event, and to rearrange order of imgs in creation or edits
    - Attach file to event -- but this can just be a cloud link
    - Message a user/group about event
    - Rich text editing. Probably a OpenSource React.Js thing that does this. But look at how emails are formatted both on desktop and on phone to indentify how `rich` the text should be
- Users:  Features that would be nice to have
	- [browse] Browes all the events of the groups you are following. Clearly this will be filterable by ClubMember, Admin, Owner
	- [browse] Block a group from posting to public feed. Iffy on the logic of this
	- [browse]Message a group 


***New Objects: Nice to have almost critical - Same from yesterday***
- Could implement but lets wait until we get this batch finished with

***Nice to have features*** wait until get this batch finished


# 12/28/19
- ToDo: Implementing groupoing of contacts and groups via tagging: tag contacts and tag groups -- implement later

Recap
- finished the interface for 26 endpoints. How many do I have to implement so far? <br>
- rough interface for API complete
- tommorrow clean up all endpoints interfaces
- tommorrow: list clearly the ToDo functions
- next: create datamodel -> implement simple CRUD API endpoint-> do it all

# 12/29/19
- Finished API. Not important to list ToDo just put them in comments
- Very good lecture last part on ElasticSearch is key: https://www.youtube.com/watch?v=nhUtZ7suZWI&t=1474s
- Currently writing down NoSQl datamodel the relational model was pretty easy. 
- iam security controls??

Notes
- ACID transactions because of normalization and tables live in diff places
- Composite Keys how to create hierarchies using sort key structure. So sort key on composite key will allow to get around expesive queurues. 
- Implement acid w/ optimisitic conccurency control - versioning

# 12/30/19
- Bought the domain tarrie.io for $40 fuck it
- Set up s3 bucket. 
    - https://s3.us-east-2.amazonaws.com/tarrie.io/users/{userId}.jpeg
        - Users can only have one profile picture
    - https://s3.us-east-2.amazonaws.com/tarrie.io/groups/{groupId}.jpeg
        - Groups can only have one profile picture
    - https://s3.us-east-2.amazonaws.com/tarrie.io/events/{eventId}/picture{i}.jpeg
        - where `i` is a integer
        - For now events can only have 2 pictures? 
    - [Public Access for S3 URl's](https://havecamerawilltravel.com/photographer/how-allow-public-access-amazon-bucket/) 
    
***Notes***
- [DataTypes DynamoDb](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.NamingRulesDataTypes.html#HowItWorks.DataTypes)
- Env Variables to create
    - `S3HostName` - s3.us-east-2.amazonaws.com/tarrie.io
         - Test: https://s3.us-east-2.amazonaws.com/tarrie.io/users/becky_b1998.jpeg
- iso format https://www.digi.com/resources/documentation/digidocs/90001437-13/reference/r_iso_8601_date_format.html
- storing address https://softwareengineering.stackexchange.com/questions/357900/whats-a-universal-way-to-store-a-geographical-address-location-in-a-database
    - https://pe.usps.com/businessmail101?ViewName=DeliveryAddress
    -  Autocomplete address - https://stackoverflow.com/questions/4169942/how-to-auto-complete-suggest-places-with-address-on-my-website-like-on-google
- TTL compares the current time in epoch time format to the time stored in the Time to Live attribute of an item. Super dope!!!
- Invarient: Tarrie handles are lower case
- Invarient: Can't join group unless invited or ask to join group
- Aggregates need streams + lambda integration
- https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-modeling-nosql-B.html

# 12/31/19
- Composite Key example: https://aws.amazon.com/blogs/database/using-sort-keys-to-organize-data-in-amazon-dynamodb/
- Event Privacy
    - When creating an event, the host can choose between the following privacy settings:
        - Private Event: 
            - Visible only to the people who are invited. 
	        - You can choose to allow guests to invite their friends. People who are invited can view the event description, photos, posts and videos.
            - Visible only to followers and group members
	        - You can choose to allow guests to invite their friends. People who are invited can view the event description, photos, posts and videos.
            - Visible only to members in a network. 
	        - Specify which groups (and all the group members) can see the event
            - You can choose to allow guests to invite their friends. People who are invited can view the event description, photos, posts and videos.
        - Public Event: Visible to anyone on or off Facebook. Anyone can see things like the event description, photos, event discussion and videos.

***Privacy Class*** Need to create this
	
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/howitworks-ttl.html

More thoughts on clustering of hashtags
- Two events are related
    - same people go
    - same people save it
    - the descriptions are similar
    - the titles are similar

So cluster on events?... No an event X has 4 hash tags z,q,e,f. Then each row would be a hastag and the columns 

- I'm not aware of an algorithm that IMHO gives satisfactory results. For a starter, compute how often tags are mentioned together, and use the strongly connected components. This is not found in "clustering" algorithms, but graph algorithms.

New IDEA!!!
- Just fucking count how often another hashtag is mentioned with hastag and rank!! Easy
- Minimum Spanning tree. With edge weights inverse of the count. Idea from here
    - https://www.cs.cmu.edu/~ckingsf/bioinfo-lectures/mst.pdf 

https://books.google.com/books?id=bJbuD2XH1_oC&printsec=frontcover&dq=social+network+analysis+economics&hl=en&newbks=1&newbks_redir=0&sa=X&ved=2ahUKEwia76DMhuHmAhXPPM0KHTSVAFUQ6AEwAHoECAAQAg#v=onepage&q=social%20network%20analysis%20economics&f=false
https://www.csc2.ncsu.edu/faculty/nfsamato/practical-graph-mining-with-R/slides/pdf/Graph_Cluster_Analysis.pdf
Clearly mapreduce. 

***Recap for today***
- Almost finished datamodel only a few more steps but hard part is done
    - https://docs.google.com/spreadsheets/d/1Z7QqBW91Jye4lVplfZYxUCWf3E1UgJoHvRGOWgsBwdU/edit?usp=sharing
- Need to come up with a privacy class for events - Asked Ryan for help on this one
- Figured out how to think about clustering hastags (count or graph theory), and find top level hashtags (graph theory centrality measure, or counts again) 
    - [pg 41-centrality](http://www.nber.org/econometrics_minicourse_2014/Jackson-NBER-slides2014_lecture1.pdf)
    - [clustering 1](https://www.csc2.ncsu.edu/faculty/nfsamato/practical-graph-mining-with-R/slides/pdf/Graph_Cluster_Analysis.pdf) 
Tommorrow finish up datamodel. Set up database. Get some functions of API working


# 1/1/20
- Google can check emails to suggest that "anyone in northwestern can view event"
- Tarrie only support "Anyone who has link can access. No sign-in required". Make this a enum so we can expand later
    - the timy url will be a random 6 character string, appended to the eventId like so: {eventId}#{randomString}
    - The endpoint will link the event to a user tarrie account if logged in, at the bottom it will have option of logging in or joining tarrie. 
        - ***join tarrie should def be prominent. This could be the main way we spread tarrie.***
        - ***Ask to enter email to be notified of changes to event*** 
	- Need to design the UI for this before we add the api. 
	- Need to design the UI for email event invites as well. 
- Also email blast invitations - email integration - calender integration

***Recap for today***
- Finished data model
- Created classes for privacy and link sharing of events basically works like event privacy on FB, and link sharing settings via Google Docs
- Created table its called "tarrie.io". The schema is super flexible so the main part is figuring out the JAVA SDK for DynamoDb
- The API actually doesn't need to be implemented just the underlying functions. The main part of the underlying functions is to implement the [`Data Model`](https://docs.google.com/spreadsheets/d/1Z7QqBW91Jye4lVplfZYxUCWf3E1UgJoHvRGOWgsBwdU/edit?usp=sharing) via Code since there isn't a schema like SQl. So function names, documentation, and the schema that the function enforce needs to be well-defined and organized since this is basically the schema. 
    - `dynamoDB_initial` <- the current branch
- Btw implemnting a chat room for a event is pretty trivial. Adding additional attributes to event like tickets is trivial -- wont implement

pretty excited to get back to coding organizaing the datamodel, and the API model via the interfaces was exhausting. Note API is just basic functionality. 

# 1/2/20
- Generating automatic id's -> https://instagram-engineering.com/sharding-ids-at-instagram-1cf5a71e5a5c
    - https://stackoverflow.com/questions/37912236/how-do-i-use-dynamodbautogeneratedkey-to-give-me-an-auto-generated-key
    - https://stackoverflow.com/questions/37072341/how-to-use-auto-increment-for-primary-key-id-in-dynamodb

- DB mapper -- https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBMapper.html
- Need to upload images to s3: 
    - https://javatutorial.net/java-s3-example
    - https://www.baeldung.com/aws-s3-java
    - https://stackoverflow.com/questions/35582224/uploading-image-to-s3-bucket-java-sdk
    - https://sysadmins.co.za/aws-java-sdk-detect-if-s3-object-exists-using-doesobjectexist/
- FIle uploads
    - https://stackoverflow.com/questions/4157127/how-to-get-mime-type-of-uploaded-file-in-jersey
    - https://stackoverflow.com/questions/12385179/how-to-send-a-multipart-form-data-with-requests-in-python
    - https://www.mkyong.com/webservices/jax-rs/file-upload-example-in-jersey/
    - https://stackoverflow.com/questions/43042805/how-to-send-a-multipart-form-data-with-requests-in-python
    - https://stackoverflow.com/questions/4526273/what-does-enctype-multipart-form-data-mean
    - https://www.w3.org/TR/html401/interact/forms.html#h-17.13.4
    - ***GOLD*** https://stackoverflow.com/questions/27609569/file-upload-along-with-other-object-in-jersey-restful-web-service
    - (JAVASCRIPT) https://developer.mozilla.org/en-US/docs/Web/API/FormData/Using_FormData_Objects
    - Getting HTTP Header - https://dzone.com/articles/jax-rs-what-is-context
        - https://stackoverflow.com/questions/16149507/obtaining-raw-request-body-in-jax-rs-resource-method
        - https://phil.tech/api/2016/01/04/http-rest-api-file-uploads/
	- (what is input stream)
    - acceptable media type {image/gif, image/jpeg, image/png}
        - `String mimeType = body.getMediaType().toString();`
	
***Recap***
- Uploaded data to DynamoDb in a clean way which is nice
- Got S3 uploads going

tommorow just build the backend code monkey type shit. btw unit testing this shit via mock is going to be annoying <br>
Also just looked at the traffic for this journal... who is cloning this shit? wtf

# 1/3/20
- [Crud operations Java/DynamoDb](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/JavaDocumentAPICRUDExample.html)
- [Java Annoations for DynamoDb](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBMapper.Annotations.html)
- [Working with Items in DynamoDB
](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/examples-dynamodb-items.html)
- [Get Item](https://docs.aws.amazon.com/code-samples/latest/catalog/java-dynamodb-src-main-java-aws-example-dynamodb-GetItem.java.html)

# 1/4/20
- [Update Item1](https://dzone.com/articles/update-dynamodb-items-withnbspjava)
- [CRUD - Update Item2](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.Java.03.html#GettingStarted.Java.03.03)
- [DynamoDb Update Expressions](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.UpdateExpressions.html)
-[Whats a document path](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.Attributes.html#Expressions.Attributes.NestedElements.DocumentPathExamples)
    - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.Attributes.html
    
***Functions to make in `Controller`***
- [X] createUser()
- [X] uploadProfileImg()
- [X] userExists(List<String> userIds)
    - [Batch Get Item](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchGetItem.html), [Java Batch Get Item](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/batch-operation-document-api-java.html)
    - Limit is 100 users. 
- [ ] addContact()
- [ ] userFollowEntity()
- [ ] userSendMessage()
	- msgType: groupJoin - user requesting to join a group
	- msgType: inbox - user is communicating with other user
	- msgType: eventInvite - user is inviting someone to a event
- [ ] groupAddMember()
- [ ] deleteUser()
- [ ] getUser()
- [ ] editUser()

***Recap***
- Created `Controller` class which contains the underlying functions that the API calls. Implemented 3 functions in the controller class and some other subsidiary functions in the `TarrieDynamoDb` & `TarrieS3` class. 
- Tommorrow continue to complete uncheck functions.
- Tommorrow implement the GSI's since [they are needed for deletes](https://stackoverflow.com/questions/52747889/how-to-update-an-item-using-partition-key-of-global-secondary-index)

Branch is still `dynamoDB_initial` in https://github.com/tarrie/io.tarrie.api

# 1/5/2020
- [conditional updates (stackoverflow)](https://stackoverflow.com/questions/28081401/dynamodbmapper-for-conditional-saves)
    - [conditional updates (aws)](https://aws.amazon.com/blogs/developer/specifying-conditional-constraints-with-amazon-dynamodb-mapper/)
- [crazy good documentation for dynamodb mapper](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/dynamodbv2/datamodeling/DynamoDBMapper.html)
- [transactions w/ mapper](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBMapper.Transactions.html)
    - [transactions w/o mapper](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transaction-example.html)

https://aws.amazon.com/blogs/developer/using-s3link-with-amazon-dynamodb/

- [ ] addContact()
- [ ] userFollowEntity()
- [ ] userSendMessage()
	- msgType: groupJoin - user requesting to join a group
	- [x] msgType: inbox - user is communicating with other user - *needed transactions*
	- msgType: eventInvite - user is inviting someone to a event
- [ ] groupAddMember()
- [ ] deleteUser()
- [ ] getUser()
- [ ] editUser()

https://egkatzioura.com/2016/10/03/query-dynamodb-items-with-dynamodbmapper/

Questions
- I have a operation that uploads a picture to s3 then updates the image path on DynamoDb... doesn't this need to be atomic? 
    - Ask Jesse or Steve


took awhile to find this error but w/ DynamoDb mapper class the getter/setter for range and pk has to be fucking exact, anything else will return a null ptr excpetion... can do whatever w/ other attributes tho. Little progresss today because of debug :(


# 1/6/20


- [Nested pojo for DynamoDb mapper](https://stackoverflow.com/questions/45023747/nested-json-structure)
- https://stackoverflow.com/questions/30793481/dynamodb-jsonmarshaller-cannot-deserialize-list-of-object

- [X] createUser()
- [X] uploadProfileImg()
- [X] userExists(List<String> userIds)
    - [Batch Get Item](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchGetItem.html), [Java Batch Get Item](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/batch-operation-document-api-java.html)
    - Limit is 100 users. 
- [x] addContact()
- [x] userFollowEntity()
- [x] groupAddMember()
- [ ] sendMessage()
	- msgType: groupJoin - user requesting to join a group
	- [x] msgType: inbox - user is communicating with other user - *needed transactions*
	- msgType: eventInvite - user is inviting someone to a event
- [x] createGroup() `(fixed)`
- [ ] deleteUser()
	- [x] transferGroupOwner()
	    - [x] getMembershipTypeOfUser()
	- [ ] check if user owns any groups then need to use transferGroupOwner() to transfer ownership to a Admin, if no Admin then a member, if no members then deleteGroup()
	- [x] batch delete w/ TarrieDynamoDb.batchWriteOutcome()
	- [ ] deleteUserEvents()
	    - delete all events the user is hosting
- [ ] deleteGroup()
- [ ] editUser()
- [ ] editroup()
- [ ] getUser()
- [ ] editUser()

Should add junit soon

- [Query and scan](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.Java.04.html)
- [Working w/ queries DynamoDb](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html)
-https://www.baeldung.com/dynamodb-local-integration-tests

[Looks like a nice Dynamo Ref](https://www.javacodegeeks.com/2017/10/amazon-dynamodb-tutorial.html) 

[batch delete](https://stackoverflow.com/questions/9154264/what-is-the-recommended-way-to-delete-a-large-number-of-items-from-dynamodb) 

[batch ops in dynamo](https://docs.amazonaws.cn/en_us/amazondynamodb/latest/developerguide/batch-operation-document-api-java.html)

[bathc again](https://github.com/aws/aws-sdk-java/blob/master/src/samples/AmazonDynamoDBDocumentAPI/quick-start/com/amazonaws/services/dynamodbv2/document/quickstart/I_BatchWriteItemTest.java)

[Querying and name map](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.Java.04.html)

# 1/7/2020
***Tips for developing***
- Swarm get the team together to hack
- Vertical slices - so backend, front-end, UI - so we can have something tesitbale
    - Whats the one simplest thing you can do?
- Payoff: whatever is a delighter. 
- Kano Model = distingusihes things that a product needs
    - Delighters - things that are different from other products - this is somethings you implement first. "This is the neat part"
    
# 1/8/20

- [ ] createEvent()
- [ ] deleteUser()
	- [x] transferGroupOwner()
	    - [x] getMembershipTypeOfUser()
	- [ ] check if user owns any groups then need to use transferGroupOwner() to transfer ownership to a Admin, if no Admin then a member, if no members then deleteGroup()
	- [x] batch delete w/ TarrieDynamoDb.batchWriteOutcome()
	- [ ] deleteUserEvents()
	    - delete all events the user is hosting

Met w/ Jesse: He mentioned concerns about consistenct and data duplication have to consider this. One case was in deleting shit: could either post to a queue for eventual deletion, or add a attribute called delete and mark a object for deleltion?

# 1/9/20

***Tips for developing***
Story panels
- Importnant because 1st time define in easy snapshot what the app is suppose to be, and show how its improves on something
- It also tells first thing going to build (panel 3)
    - (panel 3) Important because it shows the value that app is giving. The interface must be clear what value getting from it. 
    - (panel 3) What make the product distinctive. Show the power of the app, our app came to conclusion of panel 4 because `this` happened in (panel 3). 
    - Using the app is not the payoff -> solving the problem is the payof

# 1/10/20

- While working on the shitty app find-my-time came across a nice api for google cal especially the description of calendar and events. Will be useful later. https://developers.google.com/calendar/concepts/events-calendars
- Met w/ Tarzi, data duplication depends on the access patterns. If most people are reading, then pay the penality of writes for data duplication. If the amount of writes scale linearly pay more attention to the writes. Since only a select amount of people can edit a event, lets just duplicate the data for now. 
- Changes to the relationship that covers HOST, SAVED, RSVP
    - store `userInfo` in RSVP now so that in the reserve search we can easily see the user `pic` and `name` and `id` when looking at who rsvp'd to event. 
    - Created a local sort key on `startTime` so that when a user is looking at his/her event either HOST, SAVED, RSVP and look at events that are active `startTime`>= currentTime. 
    - GSI-1 is now the index to use to find all the host of a event, all the people who has saved a event, all the people who have rsvp'd event
    
    
 ***Create Event***
 - createEvent()
     - create the hostEvent item in DynamoDb -> connects to the user
     - implement inviteEntitiesToEvent() -> invites the entites listed in `private List<String> invitedEntityIds;` to the event
     - generate the hashTag entries for the event. 
 
 Need to have better fake data. 

# 1/11/20
Use atomic counter when increasing the number of rsvps
-message design was a time sink today sheesh. Trying to do conditional puts tho.

# 1/14/20
***Notes on project management***
- Everything done in slices - every day - like 2hr a day
- Bus factor - how many people hit by a bus for project to get stalled?

How to make a demo - https://courses.cs.northwestern.edu/394/demo-one.php
- Read demo task page
- Do not give a feature tour these don't tell why you wnat the product. A demo should be a story
- A demo should have a real story that shows payoff. Most of it is the payoff. Slide 4
    - before we couldnt meet, and the punchline is the thing tells use when we can meet. 
- A demo is critical for development - understand what people resonate to, missing, and what to do next. So told the story of a student that needed to learn something. Everything is driven towards how soon can tell a story. 
    - Stories are how we constantly think about design. 
    
# 1/15/20
- Need sample data . really need this wrote atleast 500lins w/o testing. this si next. fixed my 

Rando Business rules
- For now can't follow group that already a member of because by default you are already following. 

# 1/16/20
- `Controller.uploadProfileImg()`: Only updates the entity but does not update imgPath. Easy way around this is to download the default pic to the persons path. Then the path will always hav a value and we won't need to update. this is actually straight forward to do unfortunaly so lets do it :(
    - Or find a way to update all things pointing to entity so pic always up to date. 
- Any moderator can kick each moderator that joined after them (assuming everyone has full permissions).The head moderator (the first person in the list) can only leave by themselves or be removed if the sub is requested in the sub r/redditrequest if all mods are inactive from reddit for more than 60 days.
    - added time stamps maybe will implement this for now owners can kick out admins. 
    
    
***UI Design***
- (Create Event & Create Event)https://dribbble.com/shots/7376532-Skype-redesign-concept-New-Group-Chat-1-2
- (Messaging) https://dribbble.com/shots/6303653-SBS-App-Messaging/attachments

# 1/24/20
Primary color: #006400
UI Design 
- https://material.io/design/color/#
- https://material.io/design/color/the-color-system.html#tools-for-picking-colors

# 3/25/20

Set up react native. 

-Transfer to a new stack when click explore mode
- Group Name [green] . Events [greyed out]
    - use snapchat bottom nav switch. 
-Tommorow make GroupContext since some of the group info needs to be accessed in navigator for the header. 
-Finish the header for the group
- https://react-native-elements.github.io/react-native-elements/docs/searchbar.html

parallax-scroll :

# 4.6.20

- Got two endpoints up and running with ElasticBeanStalk, AWS Route 53. Have the domain api.tarrie.io working as well. 
- Finish create group event next time around for api.
-  could not annotate multipart form errors w/ tomcat so leavig that for another day
- Spent some time reorganizing front end and made group context doesnt work tho

# 4.7.20
- hooked up api to front end but only two methods getEvents, and uploadImg. Need to create end point to get the group, also need to change getEvents probs delete and use Dynamodb streams
- Next up: Use streams, lambda, sns to enable realtime shit -> https://aws.amazon.com/blogs/database/dynamodb-streams-use-cases-and-design-patterns/
    - node js sns
    - https://docs.aws.amazon.com/appsync/latest/devguide/real-time-data.html. better i think uses websocket
    - https://www.youtube.com/watch?v=olAPj6EIlag
    - following this tutorial for react native appsync dowloaded sample project but getting sleepy
        - https://docs.aws.amazon.com/appsync/latest/devguide/quickstart-write-queries.html 
	going to take some time but worth it cuz graphql gives a flexible api

# 4.9.20
### GRAPH QL

#### Resolvers
	connect the fields in a type’s schema to a data source. Resolvers are the mechanism by which requests are fulfilled
	
#### Schema's
Any field that ends w/ a exclamation point !, is a required fied. `ID!` scalar type is a unique identifier that can be either String or Int. 

**Root Types**: Query, Mutation, and Subscription, these types similar to the ones you define, but they’re different in that you expose them from your schema as the entry point for your API.

*Subscriptions* invoked as a response to a mutation. -> https://docs.aws.amazon.com/appsync/latest/devguide/real-time-data.html#aws-appsync-real-time-data	`

- Dynamidb resolver - https://docs.aws.amazon.com/appsync/latest/devguide/tutorial-dynamodb-resolvers.html
- What it means by filter - https://medium.com/open-graphql/implementing-search-in-graphql-11d5f71f179

# 4.10.20
https://github.com/aws-samples/aws-mobile-appsync-events-starter-react

# 4.11.20
- Changed api so img is uploaded first and then the user/event/group created with the imgPath inputted as a pointer. Need to reflect this change in API later

- use the green button as a next button expand it slightly to be the completion button. 
    - https://dribbble.com/shots/2352159-Daily-UI-Day-23-Onboarding
- later condense the events and use https://dribbble.com/shots/2406343-Daily-UI-Day-36-Special-Offer to expand

interesting id for cal - https://dribbble.com/shots/2414647-Daily-UI-Day-38-Calendar

-- copy this exactly for contacts - https://uimovement.com/design/3d-touch-concept-interaction/
- copy maybe exactly for expanded view of event https://uimovement.com/design/space-needle-1/

interesting way to get multiple functions - https://uimovement.com/design/add-item-dynamic/
good calandar - https://uimovement.com/design/erenovate-pro/

https://pageflows.com/user-flow/creating-things/mobile/

in create event blank out button until required shit is form

replace search with just sthe search icon- use calendar


- interesting policies - https://paperlesspost.zendesk.com/hc/en-us/articles/206609773-Setting-up-the-number-invited

next: add guest


holy crap you can just copy and paste html to email and it will just work: 

<pre style="color:#000000;background:#ffffff;"><span style="color:#800000; font-weight:bold; ">def</span> you<span style="color:#808030; ">(</span>x<span style="color:#808030; ">)</span><span style="color:#808030; ">:</span>
    <span style="color:#800000; font-weight:bold; ">print</span><span style="color:#808030; ">(</span>x<span style="color:#808030; ">)</span>
</pre>

paperless uses. RTF tho

https://superuser.com/questions/912712/how-to-send-rich-text-to-the-clipboard-from-command-line
https://stackoverflow.com/questions/23934656/javascript-copy-rich-text-contents-to-clipboard


cool but dunno how https://gfycat.com/fantasticsorrowfulintermediateegret

# 4/30/18

online experiences. So both virtual and physical. Cooking class, pottery, etc ... but need zoom integration. Nice. 

So I was watching AirBnb ceo talk and he was talking about how their virtual experience are their highest growth product. So like Airbnb came out in 2008 people were concerned about $, so they sold space in their home as a way to get revenue. This crisis can’t do that because of health concerns but people are going to want to connect. So if can make Tarrie more aligned to the new virtual world I think that’s an advantage. Like maybe some zoom integration, so people can find and host cooking classes, or meditation classes
I don’t think theirs an app that merges the virtual w/ the physical. Their a blend it’s not one or the other at least after the Covid there won’t be
Virtual concerts are meaningless, but virtual events that are more specialized and local to the physical community us are a part of I think have more value

- New emphasis on affordable travel and affordable events -- so events prices set by owner. 
- New emphasis on traveling locally not big globe trotting
- travel smaller, more localized, intimate, more human connections.

- flag system w/ ratings and reviews everything will sort itself out. 


“Tarrie is a platform that seemlessly merges the virtual and the phyical world. It provides more localized direct connections  bringing communities together, in an intemate and safe environment" 

"Allows host of events to earn money and set prices, produces high quality afforable unqiue experiences"

"Discover what's happening in ur community"

** definately put as priority integration with zoom**
or Twilio

# 05/02/20
- Refactored the options on CreateEvent so that easily add more functionality
- Partially implemented zoom virtual event, and location
- Right now working on auto complete. Tried using a github package but it was too buggy. Now using TextInput + Flatlist, what's left is 
    - styling the input in list view
    - make selected item into toucheable opacity -> transform to something usable.
    - Maybe integrate the actual maps api to show the location on the map later step not useful rt
    - rest API more expensive than node/java sdk so switch -- later step not useful rt
    - So next time styling + integrate w/ places api + if get to it {route} to previous page with address inputted. Also find out how to use address think this is an additional API or integrate w/ google/yahoo maps or something. But we need to know how to use it. 
# 07/07/2020
Finshed styling input in list view
Tried implemnting autocomplete but used nodejs, small type says use the javascript maps plugin. So need to uninstall the nodejs maps
- Also when the font is missing could be a sign of needing to run `yarn install`
# 07/08/2020
- Finished integrating goodle API also added sessionToken helps with cost
    - Added routing back to main page on selection of text or selection of location, 
    
***Need to do***
- so `CreateEventHome` takes in route.params.location now, location is missing the formatted address so cant immediately use the exported component `ActualLocation` because need to generate `main_text` and `secondary_text'.
    - Fix:  then name=main_text and secondary_text=formatted_address. See with this change can we use refactor of `ActualLocation` to replace Location info of `CreateEventHome` screen.
    
    https://maps.googleapis.com/maps/api/place/details/json?place_id=ChIJG3r5S0kVkFQR5G-wILFJDRA&key=[insert key]&language=en&fields=name,address_component,adr_address,formatted_address,geometry,url,photo,vicinity

https://maps.googleapis.com/maps/api/place/details/json?place_id=ChIJlZgEvinRD4gRiVAci0pwvB8&key=[insert key]&language=en&fields=name,address_component,adr_address,formatted_address,geometry,url,photo,vicinity

# 07/09/2020
- Functionality of location is done including the refactoring of `ActualLocation` :). Just general styling
- I think the most interesting thing would be to add virtual events so i'll start figuring that out now
- https://docs.expo.io/versions/latest/sdk/contacts/ also merge w/ apps existing contacts. Complicated :(

#### Video Chat Service Providers
- Twilio
    - https://github.com/blackuy/react-native-twilio-video-webrtc
- quickblox - react native sdk exists
    - https://quickblox.com/pricing/
    - pricing per a user, stops getting feasible at 5k. 100 conncurrent connections seems iffy but pricing seems fair
- agora - react native tutorial exist but chinese could run into problems
    - 10k free minutes every month
    - $0.00399/minute
- aws chime - react native tutorial exists 
    - Basic features are free to use and you can use them for as long as you want. These features include chat, Chime to Chime voice calls, and attending meetings. You can register for a free account and then upgrade that account to Amazon Chime Pro or add other pay-as-you-go features like Business Calling.
    - $0.0017 per attendee regardless of medium

Meh. I think just whats on the ipad. So just a login sequence for zoom, and the ability to create a meeting room. So need to see if we can schedule a meeting. However zoom scheduling needs a date (and other stuff - check macbook), so we need to create a meeting on behalf of client using info provided by user when filling in the schedule. SEEMS LIKE A SLOG THROUG https://marketplace.zoom.us/docs/api-reference/zoom-api/rooms/addaroom ZOOM API,. oh well handle later

# 07/10/2020
- Focus is on "description" "cohost" and "hashtags"
- UI https://medium.com/@ryanhalperin/ui-ux-case-study-re-designing-how-instagram-displays-hashtags-within-user-content-bc111d92bd1e
- split logical parts into sections like fb events
- create group event from fb has nice UI also
- Added Boilerplate code for HashTags, finished add info.

# 08/21/2020
- Spent 3hr figuring out why my routes werent passing to CreateEventHome. Forgot to use the CreateEventNavigator as the screen went directly to CreateEventHome. Also to pass params through a Navigator don't need context need :https://stackoverflow.com/a/61262356/13655034 . So createeventcontext is useless not even used. 
- Going to hold off on UI and try to get the basic event creation flow working including java api. Replace trashcan in top right corner with "done" 
- Left off with GroupContext and UserContext added in CreateEventHome need to use this for the finisher

# 9/30/2020
- Implemented GraphQL for create event. Excluding EventPrivacy because that shit wont come until this is more developed
- Have to implement updateItem but rt too complicated. fuck
    - Actually not that complicated: https://docs.aws.amazon.com/appsync/latest/devguide/tutorial-dynamodb-resolvers.html
    - expressionNames are subsituted with the `#` prefix, as keys
    - expressionValues are substituted in for values with the `:` prefix
    

- Need to covert send invites and hastags to AppSync as well but thats too much for now, **now that we have the rest endpoint verifying graphql input we can move on to websockets with appsync**
- Added a basic subscription in GroupHome in the front end and it works! Finally. So next clean up the server I put infront of graphql endpoint, then probably add a endpoint to actually get events, probably want to go ahead and add the global secondary index to dynAMO, a bit more testing to server, then put the logic of viewing all the events live, and creating a event and it appearing on iphone immediately. 
- Also there are some options in AWS app sync that i notice might be in https://dev.to/johanneskonings/aws-amplify-api-graphql-subscription-440d like caching
    - might be useful https://aws.amazon.com/appsync/resources/
    
Figuring out the java bug was crazy hard just code monkeyd it trying all combo's and really combing through differences till i figured it out. no documentation. Good tho cuz i want verfication (atleast most of it in server) and don't want app sync really exposed except through subscriptions. this is prob the most work ive done in a while almost a break through

remember goal is still to create a event first then upload photo to event, then according to user event is created. 

# 10/1/20
Trying to create graphql endpoints to list events and edit events

- Already had a listevent endpoint working, so added a test to make sure it worked. 
- Created GSI that just reverses pk and sk, the projected fields are pk, sk, data

Edit is actually complicates
1. Edit the main event through graphql
2. Then use GSI-2 to on prefx HOST, SAVED, RSVP. To find all users/groups that are hosting, saved, or rsvp to event, then increment `timeLastChanged` for all of these. This edit will allow all the users/groups to know that the event has changed. All they have to do is subscribe to their own ID. The ground truth is now stored only in the event. We can actually make timeLastChanged = data. Actually no, dont need to copy to gsi

To notify everyone that the event has changed. 

Or when a user logs in they can check whats under their account, and then references the ground truth to update the information. Lazy. Then set subscption on events. 


`Refactored grapgql schema, created editHostedEvent and editEvent, added these methods to TarrieAppSync, however didnt create the api endpoint or test the graphql endpoints we just created. Notice that subscription are multiple now. Also implemented change in schema as stated above make it. easier to subscribe`

# 10.3.20

Trouble with reslover, fix used `util.dynamodb.toDynamoDB` instead of `util.dynamodb.toDynamoDBJson`
ISO DATETIME FORMAT: https://www.cryptosys.net/pki/manpki/pki_iso8601datetime.html

Create 3 new functions `getAllEventRelationshipForEvent`, `_sendEventRelationshipUpdate`, `editEvent`:
- The end point under the path is basically set up no worries on that

A thing of note, MUST verifiy that a entitiy only has one type of relationship with a event. How to enforce or does it matter? Problem is that `_sendEventRelationshipUpdate` will send multiple updates to somone if it has more than one relationship. Logically it should only have one as well. how to enforce?

So `getAllEventRelationshipForEvent` uses the GSI next time need to make a test make sure it actually outputs shit and to see what it outputs. This means the fake db table needs to have a GSI. Afterthis is should be straight forward to actually implment the the endpoint. Also now we can actually create an event  with 3? api calls. One call to create event, one call to create img, another to editevent. *But we could make this into two api calls by making it mandatory to have a entity Id associated with the img, which i think is already the case*. After this setup subscriptions . After that probs fix the dummy data in GroupHome to have real date. 

# 10.4.20

TO write to a gsi mostly had it correct but have to add a annotation for the GSI sort and range key, and set consisten reads to false --> https://stackoverflow.com/questions/30457820/how-to-query-a-dynamo-db-having-a-gsi-with-only-hashkeys-using-dynamodbmapper

Also when I used the JSON mapper, not the actual keys put in as input to the api must match whateer the annoation says. For instance `Event` has a `id` field, but if i put a json with `id: EVT#xyx` api wont pick it up rather it needs to be `main_pk: EVT#xyz`
