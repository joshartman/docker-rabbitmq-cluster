Docker images to run RabbitMQ cluster. It extends the official image with a rabbitmq-cluster script that does the magic.

It is based on the version of https://github.com/harbur/docker-rabbitmq-cluster and has been modified to work with the official rabbitmq:3.7.15-management-alpine image.
(Use su-exec instead of gosu, /opt/rabbitmq instead of /usr/ and the location of docket-entrypoint.sh changed to /usr/local/bin)

# Building

Once you clone the project locally use [captain](https://github.com/harbur/captain) to build the image or do with docker:

```
docker build -t harbur/rabbitmq-cluster .
```

# Running with docker-compose

If you want to run the cluster on one machine use [docker-compose](https://github.com/docker/compose/)

```
docker-compose up -d
```

By default 3 nodes are started up this way:

```
rabbit1:
  image: harbur/rabbitmq-cluster
  hostname: rabbit1
  environment:
    - ERLANG_COOKIE=abcdefg
  ports:
    - "5672:5672"
    - "15672:15672"
rabbit2:
  image: harbur/rabbitmq-cluster
  hostname: rabbit2
  links:
    - rabbit1
  environment:
    - ERLANG_COOKIE=abcdefg
    - CLUSTER_WITH=rabbit1
    - ENABLE_RAM=true
    - RAM_NODE=true
  ports:
    - "5673:5672"
    - "15673:15672"
rabbit3:
  image: harbur/rabbitmq-cluster
  hostname: rabbit3
  links:
    - rabbit1
    - rabbit2
  environment:
    - ERLANG_COOKIE=abcdefg
    - CLUSTER_WITH=rabbit1
  ports:
    - "5674:5672"
```

If needed, additional nodes can be added to this file.

Once cluster is up:
* The management console can be accessed at `http://hostip:15672`
* The connection host should look like this: `hostip:5672,hostip:5673,hostip:5674`

# Credits

* Inspired by https://github.com/bijukunjummen/docker-rabbitmq-cluster
