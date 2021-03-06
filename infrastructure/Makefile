uid=$$(id -u $$USER)
gid=$$(id -g $$USER)
version=7
up:
	docker-compose up -d
build:
	docker-compose build --no-cache --force-rm
create-project:
	@make build
	@make up
	docker-compose exec app chown -R $(uid):$(gid) /composer
	docker-compose exec app chown -R $(uid):$(gid) /work/backend
	docker-compose exec -u $(uid):$(gid) app composer create-project --prefer-dist laravel/laravel .
	docker-compose exec app chmod -R 777 storage bootstrap/cache
install-recommend-packages:
	docker-compose exec -u $(uid):$(gid) app composer require doctrine/dbal
	docker-compose exec -u $(uid):$(gid) app composer require --dev barryvdh/laravel-ide-helper
	docker-compose exec -u $(uid):$(gid) app composer require --dev beyondcode/laravel-dump-server
	docker-compose exec -u $(uid):$(gid) app composer require --dev barryvdh/laravel-debugbar
	docker-compose exec -u $(uid):$(gid) app composer require --dev roave/security-advisories:dev-master
	docker-compose exec -u $(uid):$(gid) app php artisan vendor:publish --provider="BeyondCode\DumpServer\DumpServerServiceProvider"
	docker-compose exec -u $(uid):$(gid) app php artisan vendor:publish --provider="Barryvdh\Debugbar\ServiceProvider"
install-ja:
	docker-compose exec -u $(uid):$(gid) app php -r "copy('https://readouble.com/laravel/$(version).x/ja/install-ja-lang-files.php', 'install-ja-lang.php');"
	docker-compose exec -u $(uid):$(gid) app php -f install-ja-lang.php
	docker-compose exec -u $(uid):$(gid) app php -r "unlink('install-ja-lang.php');"
init:
	docker-compose up -d --build
	docker-compose exec app chown -R $(uid):$(gid) /composer
	docker-compose exec app chown -R $(uid):$(gid) /work/backend
	docker-compose exec -u $(uid):$(gid) app composer install
	docker-compose exec -u $(uid):$(gid) app cp .env.example .env
	docker-compose exec -u $(uid):$(gid) app php artisan key:generate
	docker-compose exec -u $(uid):$(gid) app php artisan storage:link
	docker-compose exec app php artisan migrate:fresh --seed
remake:
	@make destroy
	@make init
stop:
	docker-compose stop
down:
	docker-compose down
restart:
	@make down
	@make up
destroy:
	docker-compose down --rmi all --volumes
destroy-volumes:
	docker-compose down --volumes
ps:
	docker-compose ps
logs:
	docker-compose logs
logs-watch:
	docker-compose logs --follow
web:
	docker-compose exec web ash
app-root:
	docker-compose exec app bash
app:
	docker-compose exec -u $(uid):$(gid) app bash
migrate:
	docker-compose exec app php artisan migrate
fresh:
	docker-compose exec app php artisan migrate:fresh --seed
seed:
	docker-compose exec app php artisan db:seed
rollback-test:
	docker-compose exec app php artisan migrate:fresh
	docker-compose exec app php artisan migrate:refresh
reset:
	docker-compose exec app php artisan migrate:reset
tinker:
	docker-compose exec app php artisan tinker
test:
	docker-compose exec app vendor/bin/phpunit
optimize:
	docker-compose exec app php artisan optimize
optimize-clear:
	docker-compose exec app php artisan optimize:clear
cache:
	docker-compose exec app composer dump-autoload -o
	@make optimize
cache-clear:
	@make optimize-clear
npm:
	@make npm-install
npm-install:
	docker-compose exec web npm install
npm-dev:
	docker-compose exec web npm run dev
npm-watch:
	docker-compose exec web npm run watch
npm-watch-poll:
	docker-compose exec web npm run watch-poll
npm-hot:
	docker-compose exec web npm run hot
yarn:
	docker-compose exec web yarn
yarn-install:
	@make yarn
yarn-dev:
	docker-compose exec web yarn dev
yarn-watch:
	docker-compose exec web yarn watch
yarn-watch-poll:
	docker-compose exec web yarn watch-poll
yarn-hot:
	docker-compose exec web yarn hot
db:
	docker-compose exec db bash
sql:
	docker-compose exec db bash -c 'mysql -u $$MYSQL_USER -p$$MYSQL_PASSWORD $$MYSQL_DATABASE'
redis:
	docker-compose exec redis redis-cli
ide-helper-model:
	docker-compose exec -u $(uid):$(gid) app php artisan ide-helper:models --nowrite
ide-helper:
	docker-compose exec -u $(uid):$(gid) app php artisan clear-compiled
	docker-compose exec -u $(uid):$(gid) app php artisan ide-helper:generate
	docker-compose exec -u $(uid):$(gid) app php artisan ide-helper:meta
	@make ide-helper-model
