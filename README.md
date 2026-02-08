# gator

boot.dev "Build a Blog Aggregator in Go" project  
https://www.boot.dev/lessons/14b7179b-ced3-4141-9fa5-e67dbc3e5242

# install

- does not make sense, the binary is useless by itself, its not a self contained installer
- you need to do the configuration steps and need the `sql/schema` files to create the tables
- I guess you could use the install files, `go/pkg/mod/github.com/...`

```shell
go install github.com/scgitstuff/gator@latest
gator
```

# dependencies

## Go

https://webinstall.dev/go/

## goose

I had to run this twice, it has a bunch of dependencies

```shell
go install github.com/pressly/goose/v3/cmd/goose@latest
```

## Postgres

```shell
# install postgres
sudo apt update
sudo apt install postgresql postgresql-contrib
psql --version

# set system user password
sudo passwd postgres
boot

# start server
sudo service postgresql start
```

# configuration

## create `.gatorconfig.json`

this goes in your Home directory

```json
{
  "db_url": "postgres://postgres:postgres@localhost:5432/gator?sslmode=disable"
}
```

## create `gator` DB

```shell
sudo -u postgres psql
DROP DATABASE gator;
CREATE DATABASE gator;
# connect
\c gator
# set DB user password
ALTER USER postgres PASSWORD 'postgres';
# test
SELECT version();
# exit psql
exit
```

## create tables

```shell
cd sql/schema
goose postgres "postgres://postgres:postgres@localhost:5432/gator" down
goose postgres "postgres://postgres:postgres@localhost:5432/gator" up
```

# Run

## commands

- `register <NAME>` adds user and login
- `login <NAME>` switch to a different user
- `reset` clears the data
- `users` lists all registered users
- `addfeed "<NAME>" <URL>` add feed to the DB
- `feeds` lists feed records
- `follow <URL>` current user follows the feed matching URL
- `unfollow <URL>` current user unfollows the feed matching URL
- `following` lists all feeds the current user is following
- `agg <time>` infinite loop storing posts to the DB from any feeds added  
  https://pkg.go.dev/time#ParseDuration
- `browse <LIMIT>` display most recent posts from the feeds the user is following

# Extending the Project from the lesson

- Add sorting and filtering options to the browse command
- Add pagination to the browse command
- Add concurrency to the agg command so that it can fetch more frequently
- Add a search command that allows for fuzzy searching of posts
- Add bookmarking or liking posts
- Add a TUI that allows you to select a post in the terminal and view it in a more readable format (either in the terminal or open in a browser)
- Add an HTTP API (and authentication/authorization) that allows other users to interact with the service remotely
- Write a service manager that keeps the agg command running in the background and restarts it if it crashes

# Dev dependencies

to create the database package off of sql files

```shell
go install github.com/sqlc-dev/sqlc/cmd/sqlc@latest
sqlc version
go get github.com/google/uuid
go get github.com/lib/pq

# run any time sql files change to create wrapper code
sqlc generate
```

# TODO

- user name is case sensitive
- there was a note in the lesson about handling date format from feeds, all the tests I ran were consistent, so I didn't do any extra yet
