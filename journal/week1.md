# Week 1 — App Containerization

### Resources and Learning

- Adian Cantrill for Docker and other AWS resources - [Adian Cantrill](https://learn.cantrill.io/p/docker-fundamentals)
- [Linuxserver.io](http://Linuxserver.io) for container image registry and to learn how different types of docker images are created
- [Docker Tutorials Playlist](https://youtube.com/playlist?list=PLYxzS__5yYQlzv9_z1eZmZY8dzMlQFbaH)

### Week 1 Introduction and Notes
- This week is about Docker And App Containerization
- Keep track of your spending and billing usage on AWS and Gitpod
- Last week we created architectural diagrams, Configured AWS CLIs and set up prerequisite accounts and compelted other configurations like IAM, Billing etc
- [Week 1 journal example repo link](https://github.com/omenking/aws-bootcamp-cruddur-2023/blob/week-1/journal/week1.md)
- containerization is usefull when working in development and testing environment, it helps us to create and container and delete it when needed instead of deleting the entire work
- we can use [Linuxserver.io](http://Linuxserver.io) for references and learning how the containerization is done and we can use it while building something thats not in linuxserver.io
- Dockerhub is a container registry provided by docker where we can host docker images for private or sharing with community (works on push and pull format)
- we create and push containers so that other team members can pull it and perform development and testing on the containerized applications

- use VS code Docker extension(its a whale logo in VsCode)
- go to backend application and create a docker file (use gitpod and launch VSC in browser so that docker gets preloaded by gitpod)
- copy contents from the journal
- scratch in docker is basically an empty image (i.e. built from scratch, its basically used to build up the docker image for Debian Artifacts)
- with each command in a docker file is essentially we are building a layer step by step and adding stuff to it

like adding - debian, then add lang layer, then run some setup, install recommended libraries etc , then add python etc
(it is basically a set of commands and instructions which help build the docker container)

- in containers we use multiple layers which merge into an single unit
- when a merged file is created some files in the individual layers are not visible in the new merged layer
- Container is present inside of Host OS
- Container contains Docker work Directory i.e. */backend-flask*
- Container is for running Docker images and Guest OS is also contained in the container in the form of VM build on the Container
- difference between command and run is that command is giving a command or instruction for when to run and RUN is to run the instruction right now, while command is used to run a iunstruction when a certain event happens

# Required Homework

 > i was not able to take the screenshots of the stuff covered in the follow along, I was able to successfully configure and follow all the steps covered during the live stream 


 ### Create Notification Feature (backend and frontend)
    
   Open API
    
   - its a standard for defining API’s
   - we want to use it because it has a lot of services which are really useful like auto complete, easy to import stuff, testing etc
   - we can click on the top right icon beside the split icon to look at the (preview) documentation and example code for OpenAPI
   - we can use the website [readme.com](http://readme.com) to import your data using openapi and look at the data on the website , ex - api calls, page views, new users  etc
    
  ### Now we will start writing some code
  - open the gitpod repo and right click on docker-compose.yml file and click on docker compose up
  - once the compose finishes open the ports section to make sure that the ports are public
  - we found out that he port 3000 is not running so we go to docker extension icon and then check the containers section
  - in the container section the frontend container isnt running so we rightclik and click on logs
  - the logs are shown in the terminal and we find out “react scripts not found”
  - we go back to explorer and click on compose down to stop both the containers
  - we cd to the frontend directory and then do  “npm i”
  
  
  > 💡 you have to do this every time to run the port 3000
    
    
    
   - do compose up again
   - open the port 3000 link and we can see the cruddur app as shown below
   ![Launching the Cruddur app](assets1/week1/relaunced%20crudder%20by%20myself.png)
    
    
 ### Using the cruddur app
    
  - click on the join now and then enter your details
  - now you can enter the email again and the confirmation code is “1234”
  - we get back to the app as authenticated
  ![Authenticated and logged into the app](assets1/week1/Authenticated%20and%20logged%20into%20the%20app.png)
  
    
  #### creating the notifications section
    
   - open OpenAPi
   - go to api tab and select openapi
   - we can go to the openapi website and learn about how the commands and other things work
    
   now we want to add and endpoint for notifications tab
    
   - go to the API icon and then click on add paths in the paths section
   - we want to add a new path for the notifications section
   - it creates a new file “/name”
   - replace the /name sections on line 151 with “api/activities/notifications”
   - enter the description for the following path ('Return a feed of activity for all of those that i follow’)
   - we can also add a tag to it with the syntax “tags:”
    
``` yml
/api/activities/notifications: 
        get:
          description: 'Return a feed of activity for all of those that i follow'
          tags:
          - activities
          parameters: []
          responses:
            '200':
              description: Returns an array of activities
              content:
                application/json:
                 schema:
                  type: array
                  items:
                   $ref: '#/components/schemas/Activity'
  ```
    
   ![New path created and configured for the notification section](assets1/week1/New%20path%20created%20and%20configured%20for%20the%20notification%20section.png)
    
   - we can click on the top right corner preview button to check what the api will call for
![Checking the Api calls and responses](assets1/week1/Checking%20the%20Api%20calls%20and%20responses.png)

  ## Creating a backend endpoint for Notifications
    
   now we go back to the backend application file 
   we need to define a new end point
    
   - open app.py
   - we can look at all the services that are being used and imported
   - we can see that the services are broken down into different objects
   - its very useful to break the code into concrete services
   - if we want to break these services into microservices we can just select the service object that we need instead of looking through the entire code
   - its basically an efficient way of organisation
   - we can easily replace or modify service without disturbing the other services
    
   Changes in the app.py file
    
   - copy the following code from line 63 and plaste it below at line 68
   - then change the /home part to /notifications so that we create a app-route for notifications to get data
   - create a new notifications_activities.py file in the servicesfolder
    
    
   ![Untitled](assets1/week1/%40app%20route%20changes%20in%20app.py%20file.png)
    
   - then add the services import command at line 7 (just copy line 6 code and replace home by noitifications)
   - now open the home_activities file and copy the code to notifications_activities.py file
   - Note - make sure to change the class (LINE 2) section to NotificationsActivities in the notifications file\
   - rest of the code showing the stuff like name, likes, message etc can remain same or be edited as per your choice
   - now we can open the port 4567 and then enter /api/activities/notifications at the end of the url to get the following output
    
   ![Created new notifications backend endpoint](assets1/week1/Created%20new%20notifications%20backend%20endpoint.png)
    
  ## Creating Frontend for Notifications Tab
    
   - go to the frontend folder and open up app.js file
   - do the same thing we did for backend and create new section for notifications and sopy the code from home sections
   - the green colour line shoes the changes we made
    
   ![Changes made in the frontend file](assets1/week1/Changes%20made%20in%20the%20frontend%20file.png)
    
   - now create two new files in the pages sections with the name NotificationsFeedPage.js and NotificationsFeedPage.css
   - copy paste stuff from the homefeedpage section for both JS and CSS files of notifications
   - make sure to do the change the ‘home’ to ‘notifications’
   - reload the cruddur website and you can click on the notifications tab and see the changes there
   - make some other changes in the Notifications.js page to change everything to notifications instead of home and commit changes
   ![Notifications Tab changes Done](assets1/week1/Notifications%20Tab%20Frontend%20changes%20done.png)
   
   ## Installing Postgres and DynamoDB
   
- open gitpod
- open the docker-compose.yml file
- then copy the following code and enter into the docker-compose file

```yml
dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal
```

- then copy the other code and paste it also

``` yml
db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
```

- now paste the below code at the end of the code in the docker compose yml file

``` yml
volumes:
  db:
    driver: local
```


> 💡 [DynamoDB Local](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html) is a better and faster way to try out and run it locally , its used to test applications locally without actually accessing the DynamoDB web service


- Now do docker-compose up
- make the three ports public
- open terminal and check it the aws is installed by entering the “aws” command
- https://github.com/100DaysOfCloud/challenge-dynamodb-local
- use the above repo to get the codes and commands

### Create a Table in DynamoDB local

- enter the following code into the terminal to create a table

``` yml
aws dynamodb create-table \
    --endpoint-url http://localhost:8000 \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --table-class STANDARD
```

- enter q to exit the database

### Create an Item

- enter the following code\

``` yml
aws dynamodb put-item \
    --endpoint-url http://localhost:8000 \
    --table-name Music \
    --item \
        '{"Artist": {"S": "No One You Know"}, "SongTitle": {"S": "Call Me Today"}, "AlbumTitle": {"S": "Somewhat Famous"}}' \
    --return-consumed-capacity TOTAL
```

### List Tables

``` yml
aws dynamodb list-tables --endpoint-url http://localhost:8000
```

### Get Records

``` yml
aws dynamodb scan --table-name Music --query "Items" --endpoint-url http://localhost:8000
```

## Now install Postgres

- in order to interact with postgres we need some type of client or drivers
- we need to have a client library to interact with the server

- Go to the gitpod.yml file and copy the following code into line 11 in the gitpod.yml file to install the Postgres client into Gitpod

``` yml
- name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
```

- now run the above commands one by one into the terminal to install the postgres

- commit the changes and open the gitpod workspace again from git repo
- then make sure the PostgresSQL extension is installed (its a database explorer extension)
- you can add the extension to the gitpod.yml file so that it gets installed everytime you start up a workspace

troubleshooting psql command error

- now go to the database explorer extension
- Click on the PostgresSQL option
- then enter the connection name(anything you want)
- password is password and then click on connect
- it should show that connect success on the top

this helps us to verify that the database server connection is working

### Using Postgres

- enter “psql -Upostgres —host localhost” in terminal to open postgres
- password = password
- enter “\l” to show the list
- you will be able to see the following list

![Successfully Launched Postgres](assets1/week1/Successfully%20Launched%20Postgres.png)

- enter “\q” to exit postgres
