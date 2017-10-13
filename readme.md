# Laravel Docker

This is a modified repository of a "Dockerized" application, as covered on the [Dockerizing Your Application](https://shippingdocker.com/dockerized-app/) preview series from [shippingdocker.com](https://shippingdocker.com).


> Docker for Laravel 5.4 (Node, Postgres, PHP7.1 and Nginx)

## How to run

Make sure you have already installed [Docker](https://www.docker.com).

```bash
# Download Repository
git clone https://github.com/lkmadushan/laravel-docker.git
cd laravel-docker

# Start Laravel application (You can access application through http://localhost)
./develop up -d && ./develop composer install
```

## `develop` Helper

```bash
# Run artisan command
./develop art [command]

# Run composer
./develop composer [command]

# Run NPM commands
./develop npm [command]

# Run tests
./develop test [command]
./develop t [command]

# Run Docker Compose commands
./develop [command]
```

