# demo-crud-app Project

This project uses Quarkus, the Supersonic Subatomic Java Framework.

Also, this project implements RESTEasy with Hibernate to interact with a PostgreSQL database.

If you want to learn more about Quarkus, please visit its website: https://quarkus.io/.

You will need:
- Java 17
- Maven 3.6+
- PostgreSQL instance (_Explained below_)

## Running the PostgreSQL database

As the application needs a database to interact to.

The first thing to do is create an instance of PostgreSQL, and the easiest way to do it, is running the database
as a container:

You can run it using [Podman](https://podman.io/) or [Docker](https://www.docker.com/):

```shell
podman run --rm -d --name postgres -p 5432:5432 \
-e POSTGRES_USER=hibernate \
-e POSTGRES_PASSWORD=hibernate \
-e POSTGRES_DB=hibernate_db \
postgres:latest
```
Or
```shell
docker run --rm -d --name postgres -p 5432:5432 \
-e POSTGRES_USER=hibernate \
-e POSTGRES_PASSWORD=hibernate \
-e POSTGRES_DB=hibernate_db \
postgres:latest
```

You can notice the information about the database is declared in the command as environment variable.

Also, you can find more information about the PostgreSQL container image on the [Docker Hub page](https://hub.docker.com/_/postgres).
> For example, find options to persist the data from the database and other configurations.

## Running the application in dev mode

You can run your application in dev mode that enables live coding using:

```shell script
./mvnw compile quarkus:dev
```

## Packaging and running the application

The application can be packaged using:

```shell script
./mvnw package
```

The application is now runnable using `java -jar target/quarkus-app-runner.jar`.

## Configuring the application

The application uses the _[application.properties](/src/main/resources/application.properties)_ to configure the application:

```properties
# datasource configuration
quarkus.datasource.db-kind = postgresql
quarkus.datasource.username = ${DB_USERNAME:hibernate}
quarkus.datasource.password = ${DB_PASSWORD:hibernate}
quarkus.datasource.jdbc.url = ${DB_URL_CONNECTION:jdbc:postgresql://localhost:5432/hibernate_db}
```

You can configure the application using environment variables. If omitted the default values will be used.

> These values **must** be the same in the [PostgreSQL database](#running-the-postgresql-database)

## Running the application as container

1. Package the application as described [here](#packaging-and-running-the-application)
2. Build the Containerfile to create the image:

```shell
podman build -f Containerfile -t quarkus-crud-app
```
Or
```shell
docker build -f Containerfile -t quarkus-crud-app
```

3. Run it:

```shell
podman run --rm --name quarkus-crud-app --network lab -p 8080:8080 \
-e DB_USERNAME=hibernate \
-e DB_PASSWORD=hibernate \
-e DB_URL_CONNECTION=jdbc:postgresql://postgres:5432/hibernate_db \
quarkus-crud-app:latest
```
Or
```shell
docker run --rm --name quarkus-crud-app --network lab -p 8080:8080 \
-e DB_USERNAME=hibernate \
-e DB_PASSWORD=hibernate \
-e DB_URL_CONNECTION=jdbc:postgresql://postgres:5432/hibernate_db \
quarkus-crud-app:latest
```

> **NOTE**: If both the application and database are running as container, it is a good practice to create a network for internal communication.
> - Create it before run the application and database
>> To create the network: `podman network create lab` or `docker network create lab`
> - [Rerun the database](#running-the-postgresql-database) but now passing the network name as parameter on run of the container (`--network lab`)

## Running the application on Kubernetes

Also, you can deploy this application on a Kubernetes environment like Kubernetes itself or on Openshift for example.

To do that, just apply the definition of the Kubernetes objects contained in this project using `kubctl` or `oc client`:

```shell
kubctl apply -f src/main/kubernetes/resources.yml
```
Or
```shell
oc apply -f src/main/kubernetes/resources.yml
```

> You can deploy the PostgreSQL database on Kubernetes environment or somewhere else. But, remember to configure the URL 
> connection on [resource.yaml](/src/main/kubernetes/resources.yml) file:

```yaml
env:
  - name: DB_USERNAME
    value: hibernate
  - name: DB_PASSWORD
    value: hibernate
  - name: DB_URL_CONNECTION
    value: 'jdbc:postgresql://postgresql:5432/hibernate_db'
```

## Related Guides

- Hibernate ORM ([guide](https://quarkus.io/guides/hibernate-orm)): Define your persistent model with Hibernate ORM and
  JPA
- RESTEasy Classic ([guide](https://quarkus.io/guides/resteasy)): REST endpoint framework implementing JAX-RS and more

## To Do

1. Include instruction or files to deploy a PostgreSQL instance on Kubernetes environment;