# MariaDB Server with Wordpress

Setting up a Docker network, in order to communicate between database server, php and wordpress

<pre><code>docker network create testnet</code></pre>

Start a MariaDB server

<pre><code>
docker run -d --name mariadb-test --network testnet \
    -e MYSQL_RANDOM_ROOT_PASSWORD=1 \
    -e MYSQL_DATABASE=wp \
    -e MYSQL_USER=wpuser \
    -e MYSQL_PASSWORD=secret \
    -v myvolume:/var/lib/mysql \
    mariadb

# phpMyAdmin
docker run -d --name pma --network testnet \
    -p 8080:80 \
    -e PMA_HOST=mariadb-test \
    phpmyadmin/phpmyadmin

# WordPress
docker run -d --name wordpress-test \
    --network testnet \
    -h wordpress-test \
    -v wp-html:/var/www/html/wp-content \
    -p 8081:80 \
    -e WORDPRESS_DB_HOST=mariadb-test \
    -e WORDPRESS_DB_USER=wpuser \
    -e WORDPRESS_DB_NAME=wp \
    -e WORDPRESS_DB_PASSWORD=secret \
    wordpress
</code></pre>

**Stop and Restart Containers**

<pre><code>
docker container stop mariadb-test pma wordpress-test
docker container start mariadb-test pma wordpress-test
</code></pre>

**Update containers**

If new images of MariaDB, phpMyAdmin, and WordPress are available, you can perform a fresh installation as follow:

<pre><code>
docker container stop mariadb-test pma wordpress-test
docker container rm mariadb-test pma wordpress-test
docker pull mariadb
docker pull phpmyadmin/phpmyadmin
docker pull wordpress

docker run -d --name mariadb-test --network testnet \
    -v myvolume:/var/lib/mysql mariadb

docker run -d --name pma --network testnet \
    -p 8080:80 \
    -e PMA_HOST=mariadb-test phpmyadmin/phpmyadmin

docker run -d --name wordpress-test \
    --network testnet \
    -h wordpress-test \
    -v wp-html:/var/www/html/wp-content \
    -p 8081:80 \
    -e WORDPRESS_DB_HOST=mariadb-test \
    -e WORDPRESS_DB_USER=wpuser \
    -e WORDPRESS_DB_NAME=wp \
    -e WORDPRESS_DB_PASSWORD=secret \
    wordpress
</code></pre>

**Tidying Up**

If you want to delete all components of this containers, that is, the network as well as the containers, volumes, and images, you need to execute commands:

<pre><code>
docker container stop mariadb-test pma wordpress-test
docker container rm mariadb-test pma wordpress-test
docker volume rm myvolume
docker volume rm wp-html
docker network rm testnet
docker image rmi mariadb wordpress phpmyadmin/phpmyadmin
</code></pre>