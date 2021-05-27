### Preface
We only introduce how to import large data into graph database neo4j run on a docker container environment operated on Linux. Here, we use the official method provided by neo4j database:
````
neo4j-admin import
````
This method:
* Very fast. For data $10^6 \sim 10^7$ nodes and relations, it usually takes only several seconds to import.
* Need the datafile to be some specific form.
* Can only import the data to __*unused*__ database. Once the database is activated, data can not be imported by this method.  

### Step 1.  Setup the Neo4j database

We first pull the image of neo4j database. 
```
docker pull neo4j:enterprise
```
Here, it is __necessary__ to use the enterprise version of neo4j database. This is because we need to create different database in one container, and this function is not included in community version.  
Now, build the database container with name, say *neo4j*.
```
sudo docker run   -idt \
--env=NEO4J_ACCEPT_LICENSE_AGREEMENT=yes \
--name neo4j \
-p 7474:7474   -p 7687:7687  \
-v $HOME/workspace/neo4j/data:/var/lib/neo4j/data \
-v $HOME/workspace/neo4j/logs:/var/lib/neo4j/logs \
-v $HOME/workspace/neo4j/import:/var/lib/neo4j/import \
-v $HOME/workspace/neo4j/plugins:/var/lib/neo4j/plugins \
-v $HOME/workspace/neo4j/conf:/var/lib/neo4j/conf \
neo4j:enterprise
```

### Step 2. Make Your Datafile of the Required Form
Generally, we need two kinds of .csv files: the nodes and the relationships. 
The __nodes.csv__ should be like this:
```
personId:ID,name,:LABEL
keanu,"Keanu Reeves",Actor
laurence,"Laurence Fishburne",Actor
carrieanne,"Carrie-Anne Moss",Actor
```
and the __relationships.csv__ should be like this:
```
:START_ID,role,:END_ID,:TYPE
keanu,"Neo",tt0133093,ACTED_IN
keanu,"Neo",tt0234215,ACTED_IN
keanu,"Neo",tt0242653,ACTED_IN
laurence,"Morpheus",tt0133093,ACTED_IN
laurence,"Morpheus",tt0234215,ACTED_IN
laurence,"Morpheus",tt0242653,ACTED_IN
carrieanne,"Trinity",tt0133093,ACTED_IN
carrieanne,"Trinity",tt0234215,ACTED_IN
carrieanne,"Trinity",tt0242653,ACTED_IN
```

The *ID* should be the unique tag for each node in this graph.  
For more details, please refer to [the official guide](https://neo4j.com/docs/operations-manual/current/tools/neo4j-admin-import/).

### Step 3. Import the Data

Now, we have finished the preliminary work, and can begin to import the data.  
First we need to move the datafile into the __import__ directory of the neo4j database. Since we have set the directory projection from our host machine to our container by
```
-v $HOME/workspace/neo4j/import:/var/lib/neo4j/import
```
, we only need to move our datafile to the directory

```
/workspace/neo4j/import
```
by using the command __cp__ or  __mv__ command on linux command line.

After this, we can import the data to a new database, say *database1* by
```
docker exec --interactive --tty neo4j neo4j-admin import --database=database1 --nodes=import/nodes.csv --relationships=import/relations.csv

```
if we need to ignore the bad nodes or bad relationships, we can add the parameters
```
--skip-bad-relationships=true
```

### Step4. Create the Database
First, get in the neo4j browser, by imputing the ip address(say localhost) and the port number(say 7474).
```
http://localhost:7474
```
Normally, you will be asked to input the username and password. The default username is *neo4j* and the default password is *neo4j*. After imputing, you will be asked to set a new password.  
After logging in, we can see neo4j(default) as the default database. Now, switch the current cypher command line to __system__, and then create new database with the same name as you imported data to
```
system$ :CREATE DATABASE database1
```
Now, you can see your data imported in the database1! Congratulations!

