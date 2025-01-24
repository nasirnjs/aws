




ssh -i "nasir-dekstop-ohia.pem" -L 27017:docdb-2025-01-24-19-30-29.cluster-cryusegsgush.us-east-2.docdb.amazonaws.com:27017 ubuntu@ec2-3-14-142-76.us-east-2.compute.amazonaws.com -N




mongosh --tlsAllowInvalidHostnames --tls --tlsCAFile global-bundle.pem --username root --password pAT3T6PmnalbmRl  mongosh --retryWrites=false



from MongoDB Compass

mongodb://root:pAT3T6PmnalbmRl@127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&tlsAllowInvalidHostnames=true&tls=true&tlsCAFile=global-bundle.pem



[References 1](https://levelup.gitconnected.com/spring-boot-with-amazon-documentdb-2623d7b6cf43)

[References 2](https://www.youtube.com/watch?v=a224dfdfwgc)

