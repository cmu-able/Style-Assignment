# Installation and Setup

## Overview

This assignment consists of two separate client-server systems. One system exhibits a web-services style, the other a microservices style. 
Every attempt was made to keep these systems small and the scope of the assignment manageable. This document will describe how to download 
the required technologies and install the systems on your machine. 

## System Descriptions

Both systems use databases that contain customer information in an order database. The databases are separate, yet are structurally identical. 
Both systems are generally of the client-server style and have identical user interfaces. However, the underlying systems are structured 
differently utilizing different architectural patterns. Please view these as two completely separate systems. In the course of the assignment 
you will be asked to modify both systems and analyze which system better supported the required modifications and analyze the relative tradeoffs 
between the two different approaches. Despite the fact that all has been done to reduce complexity, setting your system up can be tricky depending 
upon how much experience you have with the technologies used. Please read the steps below before attempting installation and setup. 

This assignment makes use of the following technologies:

- MySQL Community Server – Open source database that stores all of the order and inventory data.
- J Connector – This is the Java Database Connectivity standard that allows Java programmers to access data in databases from within a Java application.
- Node.js/Javascript  – Node.js is used as a server for the web-services system. Of course Javascript is the language that Node.js servers are written in.
- Java - This is the language that is used on the client side for both systems and on the server side for the micro-services systems. We will utilize Remote Method Invocation (RMI).
- Docker - This is a containerization framework that is used to run parts of the example in a container, saving you from having to install packages directly on your machine.

## Assignment Set Up

Clone this project. Since you will have to use the command window (windows) or the terminal (MacOS), put the folder somewhere where it is 
easy to navigate to on the command line. It’s a good idea to give it a nice short name too. You should see…

`init-db` – folder: contains database template file dbtemplate.sql
`ms` – folder: microservices system source code 
`ws` – folder: web-services system source code
`Dockerfile-ws` – file: Docker build instructions for the web-services service
`Dockerfile-cs` –  file: Docker build instructions for the microservices service: CreateServices
`Dockerfile-rs` –  file: Docker build instructions for the microservices service: RetrieveServices
`Dockerfile-msc` –  file: Docker build instructions for the microservices service
`stack-ws.yml`, `stack-ws-mx.yml` – file: Docker compose for building and running the web-services server and database stack. (`-mx` for Apple Silicon Macs.)
`stack-ms.yml`, `stack-ms-mx.yml` –  file: Docker compose file for building and running the microservices server and database stack. (`-mx` for Apple Silicon Macs.)

For this assignment we will use Docker and Docker Compose for containerization. This should isolate your machine from having to install 
versions of Nodejs, or MySQL. 

## Installation of Docker

Running and installing the examples requires downloading and installing Docker, a containerization platform that works on Windows, MacOS, and Linux. Instructions for installing Docker can be found at: https://docs.docker.com/get-docker/. 

Once Docker is installed and running on your machine, you will be able to build the example systems using the instructions below.

The assignment primarily uses Docker Compose for building and setting up a virtual network between different containers. 

## Installation of MySQL Database

The examples use the MySQL Docker container, and run two instances of the container, one each for the web-services and microservices architecture. 
To ensure this runs (and data persists between runs), you need to create some docker volumes and edit some files. We give the 
instructions separately for each of the web-services and microservices examples, but the instructions are essentially the same, 
just with different files and directories.

### MySQL database preparation for web-services

1. You will need to set up the password for acccess to MySQL containing the web-services data. To do this, you will need to edit the file: `stacks-ws.yml` (`stacks-ws-mx.yml` if you are on Apple Mac). Change the follwing lines:

 ```
   MYSQL_PASSWORD: foo
   "MYSQL_ROOT_PASSWORD": foo
```
Change `foo` to be the password you want to use. 

2. You will need to create a place on your machine to store the database files, so that database information persists between runs. (Without this, 
MySQL will reset the data every time you start the example.) We will do this with Docker volumes. 
Create a Docker volume, `ws_db`, using:

```bash
$ docker volume create ws_db
```

To see that it has been created, it should appear when you issue the command:

```bash
$ docker volume ls
```

Note the if you want to start from scratch and reinitialize the database, you should remove the volume:

```bash
$ docker volume rm ws_db
```
You will receive an error if you still have containers using the volume. If there is no error, the volume name will be printed on the console. You can check the deletion with `docker volume ls`.

Note also, that the first time you run the web-services example, with an empty database, you will get errors from the container `ws_server` several times. 
This is because `ws_server` will be unable to connect during database initialization. 
Docker will automatically restart `ws_server` until the database finishes installing.

### MySQL database preparation for microservices

1. You will need to set up the password for access to MySQL containing the web services data. To do this, you will need to edit the file: `stacks-ms.yml` (`stacks-ms-mx.yml` for Apple Mac) and change the following lines:
```
   MYSQL_PASSSWORD: foo
	 ”MYSQL_ROOT_PASSWORD”: foo
```
2. Change `foo` to be the password you want to use.
You will need to create a place on your machine to store the database files, so that database information persists between runs. (Without this, MySQL will reset the data every time you start the example.) We will do this with Docker volumes. 
Create a Docker volume, `ms_db`, using:
```bash
     $ docker volume create ms_db
```
To see that it has been created, it should appear when you issue the command:
```bash
     $ docker volume ls
```
Note that if you want to start from scratch and reinitialize the database, you should remove the volume 
```bash
      $ docker volume rm ms_db
```
You will receive an error if you still have containers using the volume. If there is no error, the volume name will be printed on the console. You can check the deletion with `docker volume ls`.
Note also, that the first time you run the web-services example, with an empty database, you will get errors from the container `ms_server` several times. This is because `ms_server` will be unable to connect during database initialization. 
Docker will automatically restart `ms_server` until the database finishes installing.

## Running and testing the two systems

### Building and running web-services

The web-services example involves two containers, the mysql container and a container running node.js that provides services. The client in this case will run on the host. To build this example, you should execute:

```bash
$ docker-compose -f stack-ws.yml build
```
**OR** (for Apple Mac)

```bash
$ docker-comose -f stack-ws-mx.yml build
```

This will download the necessary base containers and build the web-services container on your machine. If you are successful you should see output from the build that looks like:

```
Successfully tagged ws_server:latest
```
OR
```
=> => naming to docker.io/library/ws_server
```

If you get some errors, check that you have changed the MySQL password as described above, and that you are connected to a network to be able to download containers and packages.

To run the example, execute the command:

```bash
$ docker-compose -f stack-ws.yml up
```
**OR**
```bash
$ docker-compose -f stack-ws-mx.yml up
```

The first time you do this (or have removed and recreated the volume), you will see some failures in ws_server that are due to MySQL initializing itself. The web-services and the database will have started successfully when you see something like:

```
ws_mysql  | Version: '5.7.33'  socket: '/var/run/mysqld/
mysqld.sock'  port: 3306  MySQL Community Server (GPL)
ws_server | Server Started at Port 3000.
```

The database and the web-services should be up and running ready to respond to clients. To ensure that the database has initialized properly, please see the section on Checking the database schema, below.

To stop the services running, in the window running docker compose, type `Ctrl-C` followed by:

```bash
$ docker-compose -f stack-ws.yml down
```
**OR**
```
docker-compose -f stack-ws-mx.yml down
```

(Alternatively, you could type the above command in another terminal and it will exit more cleanly.)

If you make any changes to the source files as part of the assignment, you should make sure to use `docker-compose ... build` again to rebuild the required containers, or javac if you make changes to the client.

#### Running the web-services client

To run the CLI client to make requests to interact with the web services, change into the ws folder in another terminal or shell window.

```bash
$ cd ws
```

The web-services client is a java program that consists of the following files.

- `WSClientAPI.java` – Abstracts the underlying communication with the Node.js server from the user interface.
- `OrdersUI.java` – Application user interface.

To compile the source code, type the following:

```bash
$ javac *.java
```

This will compile all of the source code required for the client. At this point the client is ready. To run the client, simply type:

```bash
$ java OrdersUI
```

This will give you a menu and you should be able to choose options. To test everything is ok, select `1: Retrieve all orders in the order database`.
You should see a result with no errors that looks like:

```
Retrieving All Orders::
{"Error":false,"Message":"Success","Orders":[]}
```

## Building and running microservices

Like the web-services example, the microservices example uses Docker Compose to set up the containers. Unlike the web-services example, though, three containers will be created: one each for the database and server as before, and another one for the client. The client is needed in this instance because this system uses RMI, which is difficult to use in a Docker environment, and the client needs to be part of the same virtual network as the server to work. Note that this should NOT be treated as an architectural consideration for the purposes of this assignment.

To build this example, you should execute:

```bash
$ docker-compose -f stack-ms.yml build
```
**OR** (On Apple silicon Macs)
```bash
$ docker-compose -f stack-ms-mx.yml build
```

This will download the necessary base containers and build the microservices container on your machine. If you are successful you should see output from the build that looks like:

```
Successfully tagged ms_server:latest
	…
	Successfully tagged ms_client:latest
```
Or
```
=> => naming to docker.io/library/ms_create
```
If you get some errors, check that you have changed the MySQL password as described above, and that you are connected to a network to be able to download containers and packages.

To run the example, execute the command:

```bash
$ docker-compose -f stack-ms.yml up
```
**OR** (For Apple silicon macs)
```bash
$ docker-compose -f stack-ms-mx.yml up
```

The first time you do this (or have removed the contents of the ms-db folder). The microservices and the database will have started successfully when you see something like:

```
ms_client   | 2: Retrieve an order by ID.
ms_client   | 3: Add a new order to the order database.
ms_client   | X: Exit
ms_client   |
...
ms_create   | Registered services:
ms_create   |   CreateServices
...
ms_retrieve | Registered services:
ms_mysql    | Version: '5.7.33'  socket: '/var/run/mysq...
```

The database and the microservices should be up and running ready to respond to clients, with a client machine up and running that you will log into later. To ensure that the database has initialized properly, please see the section on Checking the database schema, below.

To stop the services running, in the window running docker compose, type `Ctrl-C` followed by:
```bash
$ docker-compose -f stack-ms.yml down
```
**OR**
```bash
$ docker-compose -f stack-ms-mx.yml down
```

Alternatively, you could type the above command in another terminal and it will exit more cleanly.)

If you make any changes to the source files as part of the assignment, you should make sure to use `docker-compose ... build` again to rebuild the required containers.

#### Running the microservices client

The web-services client is a java program that consists of the following files.

- `MSClientAPI.java` – Abstracts the underlying communication with the Node.js server from the user interface.
- `OrdersUI.java` – Application user interface.
- `registry.properties` – A properties file containing the location of the microservices

To run the CLI client to make requests to interact with the web services, the process is different than for the web-services client. The docker-compose up command will have created a container for the client, so you need to connect to that and run the client code. This can be done through the `exec` command:

```bash
$ docker-compose -f stack-ms.yml exec client java OrdersUI
```

This will give you a menu and you should be able to choose options. To test everything is ok, select `1: Retrieve all orders in the order database`. You should see a result with no errors that looks like:

```
Retrieving All Orders::
[]
```

## Checking the MySQL Database Schema

To check that the MySQL database has been set up correctly, you have to connect to the database container (`ws_mysql` for the web-services example or `ms_mysql` for the microservices). For example, to check the database for web-services, type:

```bash
$ docker-compose -f stack-ws.yml exec mysql mysql -u root –p
```

(For the microservices, use `stack-ms.yml` instead). This will log you into the mysql database for the appropriate system (after you type the root password for mysql from above).

```bash
mysql> show databases;
```
You should see both the ws_orderinfo and ms_orderinfo database listed. Now verify that the tables are correct by typing:

```
mysql> use ws_orderinfo;
mysql> show tables;
```

You should see the “orders” table. Next check the table schema by typing:

```
mysql> describe orders;
```

You should see the following: 

```
+------------+------------------+------+-----+---------+----------------+
| Field      | Type             | Null | Key | Default | Extra          |
+------------+------------------+------+-----+---------+----------------+
| order_id   | int(10) unsigned | NO   | PRI | NULL    | auto_increment |
| order_date | date             | YES  |     | NULL    |                |
| first_name | varchar(20)      | YES  |     | NULL    |                |
| last_name  | varchar(20)      | YES  |     | NULL    |                |
| address    | varchar(80)      | YES  |     | NULL    |                |
| phone      | varchar(15)      | YES  |     | NULL    |                |
+------------+------------------+------+-----+---------+----------------+
```
Now we repeat this process for the `ms_orderinfo` database as follows.

```
mysql> use ms_orderinfo;
mysql> show tables;
```
Again, you should see the “orders” table. Next check the table schema by typing:

```
mysql> describe orders;
```

You should see the same schema as shown above for the ws_orderinfo/orders table. 


