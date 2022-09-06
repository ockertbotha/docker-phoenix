# docker-phoenix-dev
Creating a Dockerized Phoenix development environment.

## Step a1: Clone the Project
The default values in this project will create a basic working environment.
- Review the contents of Dockerfile
- Review the contents of docker-compose, rename as necessary

## Step a2: Build the image
``` 
$ docker-compose build 
``` 

## Step a3: create "src" directory and command "mix" alias
The "src" directory will contain the Phoenix application.
``` 
$ mkdir src
``` 
Create a "mix" alias to run the Elixir commands. 
(Only if you have not previously added the alias function from the later steps)
``` 
$ alias mix="docker-compose run --rm phoenix mix"
``` 

> The "src/" directory has been .gitignore'd, so review that file to make sure it matches your intentions

## Step a4: Initialize and configure a new Phoenix application
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
> Aliasing: Add the appropriate .alias-mix-file to the src directory

``` 
$ cd src
$ mix ecto.create
``` 

If you copied an existing application, run the database migrations now.

``` 
$ mix ecto.migrate
``` 


## Step a5: Start the application

``` 
$ cd ..
$ docker-compose up
``` 

Once started application will be available at http://localhost:4000 

## Acknowledgement
The above information is mostly from the following post: https://medium.com/swlh/use-docker-to-create-an-elixir-phoenix-development-environment-e1a81def1d2e

# Aliasing
Setting that alias from earlier is going to get old pretty quickly, also you might not want your ```mix``` command to be aliased system wide. 

Let's set things up so you can configure where it get's aliased.

## Step b1: Add the following to your Terminal Profile file
These steps have been tested using ZSH and by editing the .zshrc file.
```
mixes()
{
  # Check if a file called .alias-docker-phoenix-mix exists in the current directory
  if [ -f ./.alias-docker-phoenix-mix ] ; then
      # Run the phoenix mix command in the phoenix Docker container
      docker-compose run --rm phoenix mix "$@"
  else
      # Run the usual mix command
      mix "$@"
  fi
}

alias mix=mixes

iexes()
{
  # Check if a file called .alias-docker-phoenix-mix exists in the current directory
  if [ -f ./.alias-docker-phoenix-mix ] ; then
      # Run the elixir iex command in the phoenix Docker container
      docker-compose run --rm phoenix iex "$@"
  else
      # Run the usual mix command
      iex "$@"
  fi
}

alias iex=iexes
```

## Step b2: Create the .alias-docker-phoenix-mix file
Navigate to your project directory and create the file
```
touch .alias-docker-phoenix-mix
```

## Step b3: Apply the new configuration and test
```
# Apply Configuration
source ~/.zshrc 

# Should run in the Docker container
mix help
```

# Creating a new application
The above steps will prove that the Docker imaged Phoenix app works.

Now follow these steps to start creating your own application using this structure, please replace "new-project" with your actual-project:
## Step c1: Create your project directory
```
mkdir new-project
```
## Step c2: Configure Docker
Copy these files to your new project and confirm the contents:
- docker-compose.yml : Confirm the service, database images names and port numbers
- Dockerfile: Confirm the port numbers

## Step c3: Add the alias-phoenix to your project directory
```
# From the project root
touch .alias-docker-new-project-mix

# Updating the functions in terminal profile
mixes()
{
  # Check if a file called .alias-docker-phoenix-mix exists in the current directory
  if [ -f ./.alias-docker-phoenix-mix ] ; then
      # Run the phoenix mix command in the phoenix Docker container
      docker-compose run --rm phoenix mix "$@"
  elif [ -f ./.alias-docker-new-project-mix ] ; then
      # Run the phoenix mix command in the new-project Docker container
      docker-compose run --rm new-project mix "$@"
  else
      # Run the usual mix command
      mix "$@"
  fi
}

iexes()
{
  # Check if a file called .alias-docker-phoenix-mix exists in the current directory
  if [ -f ./.alias-docker-phoenix-mix ] ; then
      # Run the elixir iex command in the phoenix Docker container
      docker-compose run --rm phoenix iex "$@"
  elif [ -f ./.alias-docker-new-project-mix ] ; then
      # Run the elixir iex command in the new-project Docker container
      docker-compose run --rm new-project iex "$@"      
  else
      # Run the usual mix command
      iex "$@"
  fi
}

alias iex=iexes

# Apply the new Configuration
source ~/.zshrc 
```

## Step c4: Build and initialize the project
Work through Steps a2 - a5 replacing the project name where necessary and remembering you don't need the alias bit.

