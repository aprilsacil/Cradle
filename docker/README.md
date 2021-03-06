CradlePHP + Docker
==================================

# How to run #

Dependencies:

 - Docker engine v1.13 or higher. Your OS provided package might be a little old,
 if you encounter problems, do upgrade. See:
 [https://docs.docker.com/engine/installation](https://docs.docker.com/engine/installation)
 - Docker compose v1.12 or higher. See [docs.docker.com/compose/install](https://docs.docker.com/compose/install/)

Once you're done, simply `cd` to your project and run `docker-compose up`. This
will initialize and start all the containers.

## Services exposed outside your environment ##

You can access your application and the following services via **`localhost`**.

Service|Address outside containers
------|---------
Webserver|[localhost:8082](http://localhost:8082)
MySQL|**host:** `localhost`; **port:** `8084`

## Hosts within your environment ##

You'll need to configure your application to use any services you enabled:

Service|Hostname|Port number
------|---------|-----------
php-fpm|php-fpm|9000
MySQL|mysql|3306 (default)
Redis|redis|6379 (default)
Elasticsearch|elasticsearch|9200 (HTTP default) / 9300 (ES transport default)

# Docker compose cheatsheet #

**Note:** you need to cd first to where your docker-compose.yml file lives.

 - Start containers in the background: `docker-compose up -d`
 - Start containers on the foreground: `docker-compose up`. You will see a stream of logs for every container running.
 - Stop containers: `docker-compose stop`
 - Kill containers: `docker-compose kill`
 - View container logs: `docker-compose logs`
 - Execute command inside of container: `docker-compose exec SERVICE_NAME COMMAND` where `COMMAND` is whatever you want to
 run.
 - Examples:
   - Shell into the PHP container, `docker-compose exec php-fpm bash`
   - Run Cradle CLI, `docker-compose exec php-fpm bin/cradle`
   - Open a mysql shell, `docker-compose exec mysql mysql -uroot -pCHOSEN_ROOT_PASSWORD`

# Recommendations #

It's hard to avoid file permission issues when fiddling about with containers due to the fact that, from your OS point of view, any files created within the container are owned by the process that runs the docker engine (this is usually root). Different OS will also have different problems, for instance you can run stuff in containers using `docker exec -it -u $(id -u):$(id -g) CONTAINER_NAME COMMAND` to force your current user ID into the process, but this will only work if your host OS is Linux, not mac. Follow a couple of simple rules and save yourself a world of hurt.

 - Run composer outside of the php container, as doing so would install all your dependencies owned by `root` within your vendor folder.
 - Run commands (ie. `bin/cradle`) straight inside of your container. You can easily open a shell as described above and do your thing from there.
