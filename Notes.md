-- Docker is a platform for building, running and shipping applications 
-- Solves the problem - "It works on my machine" - Due to - One or more files missing - Software dependency (node, mongo, nginx etc...) or Kernel version mismatch 
-- Runs the application inside an isolated environment called "Container" 
-- A part of your application can run java 8 while another runs version 10 without messing with each other 
-- If there is need to decommission a part of an application, we can easily do that without messing with the entire application 
-- we can safely clean up our development machine with all the downloaded dependencies wihthout problems 
-- Containers and Virtual Machines 
    -- VM - abstration of a physical computer through a hypervisor (Virtualbox, VMWare) - Each machine needs an operating system that needs to be Licensed, Patched and maintanined (A lot of overhead) - slow to start - Resource Intensive (cpu, memory and disk space) 
    
    -- Containers - Same isolation like VM, but more lightweght - Needs only one Operating system that requires maintenance - Fast to load - Needs much less hardware resources 

Docker Architecture -- CLient server architecture 
  - Client talks to the Docker Engine which is the server 
  - Docker engine sits in the background and takes care of running containers 
    - containers are like special processes running on a computer 
    - Containers are created from a template called Docker image 
    - All the containers share the kernel of the Operating system 
    - A kernel is the core software of the Operating system (the part of the Operating system that manages all the applications, and hardware resources like Memory and CPU)
  
Development Workflow 

1. Package applications into a template called an "Image" using a Dockerfile 
   -- Command - Docker build 
   - An image contains 
     1. A cut-down OS 
     2. A runtime environment (java, node) 
     3. Application files 
     4. Third-party libraries 
     5. Environment variables. 
   
2. Push the image to a registry. Just like pushing your code to a git repository.
3. Pull and Start a container using an image (Container is a special process running on the machine) 
   - Docker run (Test to Production) - Run a simple image that is already published to Docker hub - hub.docker.com 
4.  Create a Dockerfile to run a simple NodeJs application 
   -- Template to build a dockerfile 
       - Start with an OS 
       - Install Node 
       - Copy app files 
       - Run the application file app.js 
            FROM node:alpine 
            RUN mkdir /app
            COPY . /app 
            WORKDIR /app 
            CMD node app.js
  -- Build the image 
      build -t hello-docker .
  -- See the image 
    docker images or docker image ls 
  -- Run the image as a container 
    docker run hello-docker
  -- Publish the image to docker hub 
    Docker push dareyregistry/hello-docker
  -- Play with docker - labs.play-with-docker.com

Hands On Excercises

1. docker pull mysql/mysql-server:latest
2. docker images ls
3. docker run --name mysql-server-container -e MYSQL_ROOT_PASSWORD=mysqlPassword -d mysql/mysql-server:8.0.27-aarch64
4. docker ps -a
5. docker exec -it mysql-server-container mysql -uroot -p
6. docker network create --subnet=172.18.0.0/24 tooling_app_network 
7. export MYSQL_PW=mysqlPassword

8.  docker run --network tooling_app_network -h mysqlserverhost --name=mysql-server -e MYSQL_ROOT_PASSWORD=$MYSQL_PW  -d mysql/mysql-server:latest 


9.  echo "CREATE USER 'clientConnect'@'%' IDENTIFIED BY 'password@@@@@';
    GRANT ALL PRIVILEGES ON * . * TO 'clientConnect'@'%';" >> ./create_user.sql


10. docker exec -i mysql-server mysql -uroot -p$MYSQL_PW < ./create_user.sql


11. docker run --network tooling_app_network --name mysql-client -it --rm mysql mysql -h mysqlserverhost -u <user-created-from-the-SQL-script> -p

--name gives the container a name
-it runs in interactive mode and Allocate a pseudo-TTY
--rm automatically removes the container when it exits
--network connects a container to a network
-h a MySQL flag specifying the MySQL server Container hostname
-u user created from the SQL script
-p password specified for the user created from the SQL script


12. export tooling_db_schema=<path-to-tooling-schema-tile>/tooling_db_schema.sql
13. docker exec -i mysql-server mysql -uroot -p$MYSQL_PW < $tooling_db_schema
14. Update the db_conn.php file with connection details to the database
$servername = "mysqlserverhost";
$username = "<user>";
$password = "<client-secret-password>";
$dbname = "toolingdb";
15. docker build -t tooling:0.0.1 .
16. docker run --network tooling_app_network -p 8085:80 -it tooling:0.0.1
17. Home-Work https://github.com/darey-devops/php-todo
18. Introduce Docker compose - Create a file, name it tooling.yaml

version: "3.9"
services:
  tooling_frontend:
    build: .
    ports:
      - "5000:80"
    volumes:
      - tooling_frontend:/var/www/html
    links:
      - db
  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: <The database name required by Tooling app >
      MYSQL_USER: <The user required by Tooling app >
      MYSQL_PASSWORD: <The password required by Tooling app >
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db:/var/lib/mysql
volumes:
  tooling_frontend:
  db:

Docker networking options 
1. Default - Docker bridge 
2. 

3.  docker-compose -f tooling.yaml  up -d 
4.  docker compose ls
5.  Kubernetes Architecture
    1.  A powerful yet very complex framework for running and orchestrating containerised application across multiple computers
    2.  The entire setup consists of Master and Slave/Worker nodes to form a single cluster
        1.  Master or Control: 
          -- Scheduling the containers on the nodes in the cluster -- Identify the right nodes to run workload
          -- Plans which of the workers is best suited to run the workload
          -- Administering the containers, and the nodes running the containers. (Tracking information about them, where they are, what resources are they using...etc)
          -- Uses different softwares to do all its work. Control plane components (Master node's Microservices)
            1.  Controller
            2.  Kube-Api-Server
            3.  etcd Database (Key- Value store)
            4.  Scheduler
        1. Slaves/Workers: Running and monitoring the containers
           1. Kubelet
           2. Kube-proxy
6.  Minikube
7.  Kubernetes the hard way
8.  Deploy tools into kubernetes 
    1.  https://gitlab.com/darey.io/pbl-expert/-/blob/master/projects/source/project22.md
    2.  https://gitlab.com/darey.io/pbl-expert/-/blob/master/projects/source/project23.md (Helm)
        1.  Deploy Jenkins 
        2.  Artifactory
        3.  Hashicorp Vault
        4.  Prometheus
        5.  Grafana
        6.  Elasticsearch ELK using ECK
        7.  Kubernetes key concepts
            1.  Services - CLusterIP, NodeIP, Loadbalancer
            2.  Difference between stateful and stateless apps
            3.  Statesfull sets, Replicasets

9.   You will write custom Helm charts
10.  Configure Ingress for all the tools and applications running in the cluster
11.  Integrate Secrets management using Hashicorp Vault
12.  Integrate Logging with ELK
13.  Inetegrate monitoring with Prometheus and Grafana
14.  Learn Jenkins as code





