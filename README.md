Building an instagram clone using Laravel + Mongo DB under Sail How to set up for a code along with [beau](https://www.youtube.com/@beau) on Youtube [Here](https://youtu.be/VK-2j5CNsvM?si=SR-kJnoIc5p3kplv).

# 1. Prerequisite
Make sure you have the following installed:
```bash
sudo apt update
sudo apt install docker.io docker-compose-plugin
```

Then allow docker user to run without `sudo`:
```bash
sudo usermod -aG docker $USER
newgrp docker
```
Also install PHP CLI and Composer:
```bash
sudo apt install php-cli unzip curl
```

# 2. Create LAravel Project with Sail
```bash
curl -s https://laravel.build/instacamp | bash
cd instacamp
```

# 3. Add MongoDB to Sail
By default Sail Ships with MySQL. You will have to add MongoDB:
## 1. Open `docker-compose.yml` .
Add the following MongoDB service under service:

```yaml
mongodb:
    image: mongo:6
    ports:
        - '${FORWARD_DB_PORT:-27017}:27017'
    volumes:
        - sail-mongo:/data/db
    networks:
        - sail
```

## 2. In the `networks` ensure:
```yaml
networks:
sail:
    driver: bridge
```
## 3. Add MongoDB volume:

```yaml
volumes:
    sail-mongo:
        driver: local
```

# 4. Install Laravel MongoDB Package
Start Sail
```bash
./vendor/bin/sail start -d
```
Install MongoDB package
```bash
./vendor/bin/sail composer require jenssegers/mongodb
```
# 5. Configure `.env` for MongoDB
```env
DB_CONNECTION=mongodb
DB_HOST=mongodb
DB_PORT=27017
DB_DATABASE=instacamp
DB_USERNAME=
DB_PASSWORD=

SESSION_CONNECTION=mongodb

CACHE_STORE=mongodb

```
NB: `DB_HOST=mongodb` should match the service name in `docker-compose.yml` .

# 6. Configure `config/database.php` & `config/cache.php`
inside your `config/database.php` add this to the `connections` array:
```php
'mongodb' => [
    'driver'   => 'mongodb',
    'host'     => env('DB_HOST', '127.0.0.1'),
    'port'     => env('DB_PORT', 27017),
    'database' => env('DB_DATABASE'),
    'username' => env('DB_USERNAME'),
    'password' => env('DB_PASSWORD'),
    'options'  => [
        'database' => env('DB_AUTHENTICATION_DATABASE', 'admin'), // optional
    ],
],
```
inside you `config/cache.php` add this in the `stores` array:
```php
'mongodb' => [
    'driver' => 'mongodb',
    'connection' => 'mongodb',
    'collection' => 'cache',
    'lock_connection' => 'mongodb',
    'lock_collection' => 'cache_locks',
    'lock_lottery' => [2,100],
    'lock_timeout'=> 86400,
],
```

# 7. Register MongoDB in `bootstarp/providers.php`.
Add the following line inside return array:
```php
MongoDB\Laravel\MongoDBServiceProvider::class,
```
The above guides laravel to use/talk to MongoDB
# 8. Restart Sail
```bash
./vendor/bin/sail down && ./vendor/bin/sail up -d
```
# 9. Code Along
Now you can proceed to code along with beau from minute 11 [here](https://youtu.be/VK-2j5CNsvM?si=SR-kJnoIc5p3kplv) Happy coding!!