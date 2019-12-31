# journal
journal for jessie

# 12/14/14

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
       
# 12/15/14
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
