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
- https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-general-nosql-design.html
- https://aws.amazon.com/blogs/database/using-sort-keys-to-organize-data-in-amazon-dynamodb/
- The first one seems nice, good amt of work to do to set DynamoDb up and understand it. I created a Admin Secret Access Keys, and had to set up new permisions. Close but no cigar lets do it tommorrow. Also need to figure out how to define the schema aka how to structure dynamodb. Store the schema in the ressource folder? idk. Right now i think evrything looks pretty organized. gitignore did not ignore my access keys so got secuirty risk emails from AWS oh well, crazy how fast they found my github and the access keys though. sheehs!
    - https://github.com/tarrie/io.tarrie.api/pull/1 -- contains the brANCH that i'll be working with. 
