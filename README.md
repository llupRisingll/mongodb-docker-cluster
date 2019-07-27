# MongoDB Cluster on Docker - Boilerplate
Deploying MongoDb ReplicaSet Cluster using Docker. Docker must be installed.

### To run the application:
```
docker-compose up
```

### Connect to the primary node
```
docker-compose exec init-mongo-primary mongo -u "root" -p "password"
```

### Instantiate the replica set
```
rs.initiate({"_id" : "init-replica-set","members" : [{"_id" : 0,"host" : "init-mongo-primary:27017"},{"_id" : 1,"host" : "init-mongo-worker-1:27017"},{"_id" : 2,"host" : "init-mongo-worker-2:27017"},{"_id" : 3,"host" : "init-mongo-worker-3:27017"}]});
```

### Set the priority of the master over the other nodes
```
conf = rs.config();
conf.members[0].priority = 2;
rs.reconfig(conf);
```

### Create a cluster admin
```
use admin;
db.createUser({user: "cluster_admin",pwd: "password",roles: [ { role: "userAdminAnyDatabase", db: "admin" },  { "role" : "clusterAdmin", "db" : "admin" } ]});
db.auth("cluster_admin", "password");
```

### Create a collection on a database
```
use user_test_db;
db.createUser({user: "my_user",pwd: "password",roles: [ { role: "readWrite", db: "user_test_db" } ]});
db.createCollection('my_collection');
```

### Verify credentials
Make sure to exit from the primary node before proceeding. Use `exit` to exit.
```
docker-compose exec init-mongo-primary mongo -u "my_user" -p "password" --authenticationDatabase "user_test_db"
```

### Destroy the cluster
```
docker-compose down
```