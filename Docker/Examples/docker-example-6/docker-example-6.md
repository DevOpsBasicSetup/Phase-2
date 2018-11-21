#### Step-1: Run the mysql db container

    docker run -d --name db -e MYSQL_ROOT_PASSWORD=simple -e MYSQL_DATABASE=firstdb -e MYSQL_USER=user -e MYSQL_PASSWORD=pass123 mysql

#### Step-2: Connect to the db container and create tables

    docker exec -it db bash

    mysql -h 127.0.0.1 -P 3306 -uuser -ppass123 firstdb

    CREATE TABLE Persons (PersonID int, LastName varchar(255), FirstName varchar(255), Address varchar(255), City varchar(255) );

    INSERT INTO Persons (PersonID, LastName, FirstName, Address, City) VALUES (1, 'Sureshkumar', 'Deepak', 'Jackal Creek','Johannesburg');

#### Step-3: Java file to connect to database. Copy this fie into your docker server where you want to build docker image from dockerfile.

    [DockerConnectMySQL.java](DockerConnectMySQL.java)

#### Step-4: Docker file

    FROM java:8
    COPY . /
    WORKDIR /
    RUN wget http://central.maven.org/maven2/mysql/mysql-connector-java/8.0.13/mysql-connector-java-8.0.13.jar
    RUN javac DockerConnectMySQL.java
    CMD ["java", "-classpath", "mysql-connector-java-8.0.13.jar:.","DockerConnectMySQL"]


#### Step-5: Build the Dockerfile

    docker build -t javadbapp .

#### Step-6: Run the docker image

    docker run --name linkcontainers --link db javadbapp
    
    Output:
    root@ip-172-31-46-207:~# docker run --name linkcontainers --link db javadbapp
    Loading class `com.mysql.jdbc.Driver'. This is deprecated. The new driver class is `com.mysql.cj.jdbc.Driver'. The driver is automatically registered via the SPI and manual loading of the driver class is generally unnecessary.
    Connecting to database...
    ID: 1
    , First: Deepak
    , Last: Sureshkumar
    , Address: Jackal Creek
    , City: Johannesburg
