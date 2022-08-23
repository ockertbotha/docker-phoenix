# docker-phoenix-dev
Creating a Dockerized Phoenix development environment.

## Step 1: Clone the Project
The default values in this project will create a basic working environment.
- Review the contents of Dockerfile
- Review the contents of docker-compose


## Step 2: Build the image
``` 
$ docker-compose build 
``` 

## Step 3: create "src" directory and command "mix" alias
The "src" directory will contain the Phoenix application.
``` 
$ mkdir src
``` 
Create a "mix" alias to run the Elixir commands.
``` 
$ alias mix="docker-compose run --rm phoenix mix"
``` 



## Step 4: Initialize and configure a new Phoenix application
This command will create a new Phoenix apllication called "hello" under the "./src" directory, which will be mounted inside the container under "/app" (the default working directory).

``` 
$ mix phx.new . --app hello
``` 


In the configuration file `src/config/dev.exs` change the database hostname and endpoint IP.

``` 
...
# Configure your database
...
hostname: "db",
...
config :hello, HelloWeb.Endpoint,
  # Binding to loopback ipv4 address prevents access from other machines.
  # Change to `ip: {0, 0, 0, 0}` to allow access from other machines.
  http: [ip: {0, 0, 0, 0}, port: 4000],
...
``` 

Initialize the database with Ecto

``` 
$ src
$ mix ecto.create
``` 

If you copied an existing application, run the database migrations now.

``` 
$ mix ecto.migrate
``` 


## Step 5: Start the application

``` 
$ cd ..
$ docker-compose up
``` 

Once started application will be available at http://localhost:4000 

## Acknowledgement
This information is mostly from the following post: https://medium.com/swlh/use-docker-to-create-an-elixir-phoenix-development-environment-e1a81def1d2e
