WEBAPP_DIR = /var/www/website
FRONT_IMAGE_NAME = vojtabiberle/apache-php7
MYSQL_IMAGE_NAME = vojtabiberle/alpine-mariadb
COMPOSER_IMAGE_NAME = vojtabiberle/composer

DB_USER = user
DB_PASSWORD = secret
DB_NAME = db
DB_DATABASES = db

FRONT_IMAGE_ID = $$(docker ps | grep ${FRONT_IMAGE_NAME} | awk '{print $$1}')
MYSQL_IMAGE_ID = $$(docker ps | grep ${MYSQL_IMAGE_NAME} | awk '{print $$1}')
DOCKER_HOST_IP = $$(ip route | grep 'src' | awk '/docker/ { print $$9 }')

environment:
	echo "WEBAPP_DIR: ${WEBAPP_DIR}"
	echo "FRONT_IMAGE_NAME: ${FRONT_IMAGE_NAME}"
	echo "MYSQL_IMAGE_NAME: ${MYSQL_IMAGE_NAME}"
	echo "COMPOSER_IMAGE_NAME: ${COMPOSER_IMAGE_NAME}"
	echo "DB_USER: ${DB_USER}"
	echo "DB_PASSWORD: ${DB_PASSWORD}"
	echo "DB_NAME: ${DB_NAME}"
	echo "FRONT_IMAGE_ID: ${FRONT_IMAGE_ID}"
	echo "MYSQL_IMAGE_ID: ${MYSQL_IMAGE_ID}"
	echo "DOCKER_HOST_IP: ${DOCKER_HOST_IP}"

build:
	docker-compose build $(PARAMS)

up:
	docker-compose up -d $(PARAMS)

stop:
	docker-compose stop $(PARAMS)

run:
	docker-compose run -d --service-ports front $(PARAMS)

webserver-sh:
	docker exec -it ${FRONT_IMAGE_ID} /bin/sh

webserver-cat-errors:
	docker exec -it ${FRONT_IMAGE_ID} /bin/cat /var/log/apache2/error.log

mysql-console:
	mysql -u${DB_USER} -p${DB_PASSWORD} ${DB_NAME} --protocol=TCP $(PARAMS)

mysql-dump:
	mysqldump -u${DB_USER} -p${DB_PASSWORD} --protocol=TCP --databases ${DB_DATABASES} $(PARAMS)

composer:
	docker-compose run composer /sbin/my_init --skip-startup-files --quiet -- /sbin/setuser webapp /usr/bin/composer $(PARAMS)

fix-perms:
	sudo chown -R $$USER:$$USER ./
	sudo find ./ -type d ! -perm 755 -exec chmod 755 {} \;
	sudo find ./ -type f ! -perm 644 -exec chmod 644 {} \;
	sudo chmod -R 777 ./data
	sudo chmod -R 777 ./htdocs/data
	sudo chmod -R 777 ./logs
	sudo chmod 755 ./
	sudo chmod 775 deploy.sh dev.sh get-access-token.sh import.sh maker mysql-prod-to-devel.sh mysql.sh php.sh production-dump.sh rollback.sh run.sh
	sudo find ./scripts -name \*.sh -exec chmod 775 {} \;

nodejs:
	docker-compose run nodejs $(PARAMS)

npm:
	docker-compose run nodejs npm $(PARAMS)

gulp:
	docker-compose run nodejs gulp $(PARAMS)

gulp-watch:
	docker-compose run -d nodejs gulp watch $(PARAMS)

cli:
	docker exec -it ${FRONT_IMAGE_ID} /bin/sh -c 'cd ${WEBAPP_DIR} && /usr/bin/php ${WEBAPP_DIR}/htdocs/index.php $(PARAMS)'

xdebug:
	docker run --add-host dockerhost:${DOCKER_HOST_IP} --rm --link ${MYSQL_IMAGE_ID}:mysql -v `pwd`:${WEBAPP_DIR} -e PHP_IDE_CONFIG="serverName=localhost" -e XDEBUG_CONFIG="idekey=PHPSTORM remote_host=dockerhost remote_port=9000" ${FRONT_IMAGE_NAME} /bin/sh -c 'cd ${WEBAPP_DIR} && /usr/bin/php ${WEBAPP_DIR}/htdocs/index.php $(PARAMS)'

migrations-migrate:
	make cli PARAMS="migrations:migrate --no-interaction"

migrations-generate:
	make cli PARAMS="migrations:generate"

codecept:
	docker exec -it ${FRONT_IMAGE_ID} /bin/sh -c 'cd ${WEBAPP_DIR} && /usr/bin/php ${WEBAPP_DIR}/vendor/bin/codecept $(PARAMS)'

codecept-xdebug:
	docker run --add-host dockerhost:${DOCKER_HOST_IP} --rm --link ${MYSQL_IMAGE_ID}:mysql -v `pwd`:${WEBAPP_DIR} -e PHP_IDE_CONFIG="serverName=localhost" -e XDEBUG_CONFIG="idekey=PHPSTORM remote_host=dockerhost remote_port=9000" ${FRONT_IMAGE_NAME} /bin/sh -c 'cd ${WEBAPP_DIR} && /usr/bin/php ${WEBAPP_DIR}/vendor/bin/codecept $(PARAMS)'

ps:
	docker ps $(PARAMS)

psa:
	docker ps -a $(PARAMS)

stats:
	docker stats $$(docker-compose ps -q) $(PARAMS)

kill:
	docker stop $$(docker-compose ps -q) $(PARAMS)

rm:
	docker rm $$(docker-compose ps -q) $(PARAMS)

help:
	echo "This is basic makefile for ZF2 with Doctrine2"
	echo ""
	echo "Usage:"
	echo "You can run this makefile directly with Make, but better is use maker script!"
	echo ""
	echo "Available commands:"
	echo "build - create all necessary docker images"
	echo "up - recreate and run allimages"
	echo "stop - stop all images"
	echo "run - run all images"
	echo "webserver-sh - shell in webserver container"
	echo "webserver-cat-errors - cat apache error log"
	echo "mysql-console - connect to mysql/mariadb"
	echo "mysql-dump - print dump of selected databases"
	echo "composer \$$PARAMS - runs composer with params"
	echo "fix-parms - fix permitions - uses sudo, don't be scare and look at command"
	echo "nodejs \$$PARAMS - runs node.js inside docker"
	echo "gulp \$$PARAMS - runs gulp inside docker"
	echo "gulp-watch - runs gulp watch task inside docker"
	echo "cli - run ZF2 cli command"
	echo "xdebug - run ZF2 cli command with xdebug enabled"
	echo "migrations-migrate - run migrations"
	echo "migrations-generate - generate new migration file"
	echo "codecept - run codeception"
	echo "codecept-xdebug - run codeception with xdebug enabled"
	echo "ps - show all containers"
	echo "psa - show all images"
	echo "stats - show stats for current running containers"
	echo "kill - stop all currently running containers"
	echo "rm - delete all containers"

name:
	echo "project"

version:
	echo "1.0.0"
