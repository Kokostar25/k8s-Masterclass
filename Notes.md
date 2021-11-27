1. docker pull mysql/mysql-server:latest
2. docker images ls
3. docker run --name <container_name> -e MYSQL_ROOT_PASSWORD=<my-secret-pw> -d mysql/mysql-server:latest 
4. docker ps -a
5. docker exec -it <container_name> mysql -uroot -p
6. docker network create --subnet=172.18.0.0/24 tooling_app_network 
7. export MYSQL_PW=<root-secret-password>
8. docker run --network tooling_app_network -h mysqlserverhost --name=mysql-server -e MYSQL_ROOT_PASSWORD=$MYSQL_PW  -d mysql/mysql-server:latest 
9. cat CREATE USER '<user>'@'%' IDENTIFIED BY '<client-secret-password>';
    GRANT ALL PRIVILEGES ON * . * TO '<user>'@'%'; >> ./create_user.sql

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

19. docker-compose -f tooling.yaml  up -d 
20. docker compose ls
21. Kubernetes the hard way
22. Deploy tools into kubernetes 
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

23.  You will write custom Helm charts
24.  Configure Ingress for all the tools and applications running in the cluster
25.  Integrate Secrets management using Hashicorp Vault
26.  Integrate Logging with ELK
27.  Inetegrate monitoring with Prometheus and Grafana
28.  Learn Jenkins as code





