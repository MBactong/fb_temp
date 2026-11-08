# FishBase Docker

Dockerized FishBase website including revamped Version 4.

# Table of Contents

- [Prerequisite](https://github.com/FiN-FBSLB/fb_slb_serverbuild#prerequisite)
    - [Docker Installation](https://github.com/FiN-FBSLB/fb_slb_serverbuild#docker-installation)
    - [Other Software Installation](https://github.com/FiN-FBSLB/fb_slb_serverbuild#other-software-installation)
- [Usage](https://github.com/FiN-FBSLB/fb_slb_serverbuild#usage)
    - [Directory and file structure](https://github.com/FiN-FBSLB/fb_slb_serverbuild#directory-and-file-structure)
    - [The environment variables](https://github.com/FiN-FBSLB/fb_slb_serverbuild#the-environment-variables)
- [MySQL](https://github.com/FiN-FBSLB/fb_slb_serverbuild#mysql)
    - [Things to do and check before creating mysql container.](https://github.com/FiN-FBSLB/fb_slb_serverbuild#things-to-do-and-check-before-creating-mysql-container)
    - [Things to do and check after mysql container is created.](https://github.com/FiN-FBSLB/fb_slb_serverbuild#things-to-do-and-check-after-mysql-container-is-created)
    - [Updating a database](https://github.com/FiN-FBSLB/fb_slb_serverbuild#updating-a-database)
- [ElasticSearch](https://github.com/FiN-FBSLB/fb_slb_serverbuild#elasticsearch)
    - [Build and create the container](https://github.com/FiN-FBSLB/fb_slb_serverbuild#build-and-create-the-container)
    - [Create the fbsearch index](https://github.com/FiN-FBSLB/fb_slb_serverbuild#create-the-fbsearch-index)
- [Logstash](https://github.com/FiN-FBSLB/fb_slb_serverbuild#logstash)
    - [Build and run the image and container.](https://github.com/FiN-FBSLB/fb_slb_serverbuild#build-and-run-the-image-and-container)
    - [Check fbsearch back in fb-elastic.](https://github.com/FiN-FBSLB/fb_slb_serverbuild#check-fbsearch-back-in-fb-elastic)
- [FishBasev3](https://github.com/FiN-FBSLB/fb_slb_serverbuild#fishbase-v3.5)
    - [Clone FishBase](https://github.com/FiN-FBSLB/fb_slb_serverbuild#clone-fishbase)
    - [Possible Errors](https://github.com/FiN-FBSLB/fb_slb_serverbuild#possible-errors)
- [FishBase v4](https://github.com/FiN-FBSLB/fb_slb_serverbuild#fishbase-v4) 
    - [Clone FishBasev4](https://github.com/FiN-FBSLB/fb_slb_serverbuild#clone-fishbasev4)
    - [Update Libraries](https://github.com/FiN-FBSLB/fb_slb_serverbuild#update-libraries)
    - [Update env](https://github.com/FiN-FBSLB/fb_slb_serverbuild#update-env)
- [Proxy](https://github.com/FiN-FBSLB/fb_slb_serverbuild#proxy)
- [Assets](https://github.com/FiN-FBSLB/fb_slb_serverbuild#assets)


# Prerequisite 
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)


### Docker Installation
[<div dir="rtl">Back to top</div>](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

For Windows and development environments use [WSL2](https://docs.microsoft.com/en-us/windows/wsl/install).  
For Linux servers and production environment install [Docker engine](https://docs.docker.com/engine/install/) and [docker-compose](https://docs.docker.com/compose/install/linux/#install-using-the-repository) according to the official Docker documentation. If you follow the [create your own repository route](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository) as describe in the docker website, be sure to grant read and execute permission for the docker.gpg and its directory.

Check your installations by checking its versions.

```
$ sudo docker version
```
You should see something like this: <br />

![dockerengine](https://user-images.githubusercontent.com/34087729/192432800-51765f54-d0ad-44b4-8e94-acbbdfd2700f.JPG)


```
$ sudo docker compose version
```
![docker compose](https://user-images.githubusercontent.com/34087729/192433042-4d15110b-2ae7-479c-b7fa-20175c52b17b.JPG)

### Other Software Installation
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

Acquire your [github personal acces token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token). This token will serve as password when using git process. 

Install Git, 7zip and a text editor in your server if not yet done. <br />
```
$ sudo apt update  
$ sudo apt install Git 
$ sudo apt install p7zip-full
$ sudo apt install vim
```
<br />

Prepare your server directory for cloning fb_slb_serverbuild repo. Make sure that the users and groups have proper permissions in the given directory. For this example, we will use /www as the main directory to host the webfiles. 


# Usage
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

cd to the server directory where you intend to host your files
```
$ cd /www
```

clone this serverbuild repository

```
$ sudo git clone https://github.com/FiN-FBSLB/fb_slb_serverbuild.git
Enter your username
Enter your personal access token
```

A new directory named fb_slb_serverbuild will be created in /www directory. cd to this newly created directory: 

```
$ cd fb_slb_serverbuild
```

### Directory and file structure
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

The file structure of this repo is given below:
```
├── .env.example
├── README.md
├── docker-compose.yml
├── elasticsearch/
│   ├── Dockerfile
│   ├── conf/
│   └── data/
├── fishbasev3/
│   ├── Dockerfile
│   ├── conf/
│   └── site/
├── fishbasev4/
│   ├── Dockerfile
│   ├── conf/
│   └── site/
├── logstash/
│   ├── Dockerfile
│   ├── config/
│   └── pipeline/
├── mysql/
│   ├── Dockerfile
│   ├── conf/
│   ├── data/
│   └── dump/
└── proxy/
    ├── Dockerfile
    ├── awstats/
    ├── letsencrypt/
    ├── log/
    ├── scripts/
    └── templates/
```
Copy .env.example to .env to create a specific .env file for your server.
```
$ cp .env.example .env
```
Use a text editor to edit its entries. 
```
$ sudo vim .env
```
### The environment variables
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)


| Variable | Type | Description |
| ------ | ------ | ------ |
| COMPOSE_CONVERT_WINDOWS_PATHS | bool | Convert Windows paths to Unix paths. (If in doubt set it to 1.) |
| TIMEZONE | string | Set the correct time zone of the server. E.g. "Europe/Vienna"(remove quotes in the actual file) |
| VOLUMES_DRIVER | string | will serve as a shorthand name for volumes in the docker-compose file |
| MYSQL_ROOT_PASSWORD | string | Root password of the mysql database. |
| MYSQL_USER | string | User to be used for database connection. |
| MYSQL_PASSWORD | string | Password to be used for database connection. |
| DATA_PATH_HOST | string | directory in the host computer where mysql data will be stored |
| SITE_DOMAIN | string | FQDN of the Host. |
| SITE_PROTOCOL | string | Protocol/scheme to be used. (If in doubt set it to "https".) |
| SITE_PATH_V3 | string | Under which path FishBase V3 should be available. Keep it set to "/" as V3 is not aware to be run in subdirectory. |
| SITE_PATH_V4 | string | Under which path FishBase V4 should be available. E.g. "/v4" |
| CERTBOT | bool | Install and run certbot to generate a Let's Encrypt certificate. (If in doubt set it to 1.) |
| CERTBOT_DOMAIN | string | Comma seperated list of domains for which the certificate should be generated. Eg. "www.fishbasetest.com,fishbasetest.com" |
| CERTBOT_EMAIL | string | Contact email address for receiving Let's Encrypt emails/notifications. |



The startup sequence should be:
1. mysql
2. elasticsearch
3. logstash
4. fishbasev3
5. fishbasev4
6. proxy

# MySQL
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

### Things to do and check before creating mysql container.
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)
- Make sure .env file is properly filled-up
- Declared variable in .env file should match with the one in docker-compose.yml
- The directory in DATA_PATH_HOST should be empty
- Place all SQL files /mysql/dump directory together with the zgrantall.sh. 
- Make sure you have all the necessary permission to read and execute the sql files.
- Make sure you are in the directory where docker-compose.yml is located

Build the mysql image by issuing the command:
```
$ sudo docker compose build mysql
```
If the image building is successful, you should see something like the image below:  <br />
![buildmysql](https://user-images.githubusercontent.com/34087729/192440778-f18ef61c-e362-4aff-bc20-c6ebbe9d294a.JPG)

You can now create the mysql container.

```
$ sudo docker compose up -d mysql
```

Too see if the mysql container is successfuly created run the command:
```
$ sudo docker container ls -a
```
It should show all the containers created <br />

![mysql](https://user-images.githubusercontent.com/34087729/192442889-ecd40e6e-7773-4391-b744-d51c0e44f74c.JPG)


First run of mysql data takes a long time (around 3-4 hours). The -d in the command above lets docker run the processes in daemonize or background mode.

To know if what stage the process is already in, you can check by issuing the command:

```
$ sudo docker logs fb-mysql 
```

If all the processes are done, a line in the log files will indicate that the container is ready for connection.

![ready for connection](https://user-images.githubusercontent.com/34087729/192443544-4a52fd88-8796-4ac7-9b12-d6034116fe33.JPG)

### Things to check after mysql container is created.
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)
- Check if non-root user and password are working.


Access the fb-mysql container:
```
$ sudo docker exec -it fb-mysql bash 
```
Enter mysql server by using the credential you entered in the .env file, <MYSQL_USER> <MYSQL_PASSWORD>

```
root@e2a08fb30d8e:/# mysql -u <MYSQL_USER> -p
enter password:<MYSQL_PASSWORD>
```
If the creation of non-root user is successful, you are now ready to access the mysql server inside the fb-mysql container.

![mysqlwelcome](https://user-images.githubusercontent.com/34087729/192445776-05f80e86-4c9d-4a64-89bf-3b5e8ca392a7.JPG)

- Check if all databases are present

```
show databases;
```
Don't forget the ; at the end of each command. <br />

![databases](https://user-images.githubusercontent.com/34087729/192446028-e7b6d06e-64f6-4535-a638-00018ba045ff.JPG)

### Updating a database
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

- Copy the database dump/sql file to mysql/data, be sure to have the correct permissions and ownership.
- access the mysql container and enter the mysql server within the container
- use the database that needs to be updated
```
use <database name>
```
Indicate its source
```
source <database_name>.sql
```
![update](https://user-images.githubusercontent.com/34087729/192468128-6faa2496-8d1a-4e11-bc69-3309d033dada.JPG)

Alternatively, you can run the command below to update a database:
```
sudo docker exec -i fb-mysql sh -c 'exec mysql -u"$MYSQL_USER" -p"$MYSQL_PASSWORD" $databasename' < "$databasesource"
```

# Elasticsearch
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

The elasticsearch container is dependent on the fb-mysql. You need to have a working fb-mysql container before you can proceed. <br />
In the process of building fb-elastic, it will create ${VOLUMES_DRIVER}/elasticsearch/data directory if it doesn't exist yet. 
Please make sure you have both read and write permission to this folder.

### Build and create the container
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

Please make sure you are in the directory where the docker-compose.yml is located and build the image by issuing the command below:
```
$ sudo docker compose build elasticsearch
```
A succesful image build should look like the image below. <br />

![build elasticsearch](https://user-images.githubusercontent.com/34087729/192497566-1f80c242-44cf-4631-a848-303d020dfd35.JPG)

Build the fb-elastic container. 
```
$ sudo docker compose up -d elasticsearch
```
The image below shows the log file of a successfuly created fb-elastic container. Use the command below to access the log file.

```
$ sudo docker logs fb-elastic
```
![upelasticsearch](https://user-images.githubusercontent.com/34087729/192503890-cae4883c-ede5-4e19-b569-33b0109869cb.JPG)

### Create the fbsearch index
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

After successfuly creating the fb-elastic container you now need to create the fbsearch index.
To access the container use the following command:
```
$ sudo docker exec -it fb-elastic bash
```
Check if there are indexes:
```
curl http://localhost:9200/_aliases?pretty=true
```
If it is newly created container, it is expected that there will be no indexes. <br />
Create the fbsearch index
```
curl -s -XPUT -H "Content-Type: application/json" "http://localhost:9200/fbsearch" -d @/fbsearch-settings.json
```
A successful creation of index will be confirmed by:

```
{"acknowledged":true,"shards_acknowledged":true,"index":"fbsearch"}
```
You can check again the existence of fbseach index by issuing the command:
```
curl http://localhost:9200/_aliases?pretty=true
```
You can also check the structure of fbsearch index by the following script:
```
curl http://localhost:9200/fbsearch/_mappings?pretty=true
```
The structure of fbsearch index is shown in the image below: <br />

![elasticmapping](https://user-images.githubusercontent.com/34087729/192512044-c7d7f51a-8aed-4dc1-96a9-3ddb6c3b07cf.JPG)

If all is well on both fb-mysql and fb-elastic, we are now ready to populate the fbsearch index of elasticsearch.

# Logstash
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

The Logstash container is a server-side data processing pipeline that will transform the database records from our fb-mysql container to an elasticsearch index.
To run logstash, we need to have running fb-mysql and fb-elastic. This container only needs to run when there is new data that needs indexing (every mirror update).

### Build and run the image and container.
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)
```
$ sudo docker compose build logstash
```
Then run the container
```
$ sudo docker compose up logstash
```
Succesful indexing would look like the image below:
![logstashup](https://user-images.githubusercontent.com/34087729/192569053-edf8c1b1-e632-4cd7-b127-b2ea9071bf4f.JPG)

Hit Ctrl+c to stop the container. Now the fbsearch index should be populated with records.

### Check fbsearch back in fb-elastic
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

``` 
$ sudo docker exec -it fb-elastic bash
curl -X GET http://localhost:9200/fbsearch/_search?q=%22gadus%20morhua%22?pretty=true
```
The command above will give the result below:

![result](https://user-images.githubusercontent.com/34087729/192580165-e61d57c4-634c-48bf-acb7-238c2e1e48bd.JPG)
At this stage of the process we are at least clear that our data sources, mysql and elastic search, are working and properly populated. We are now ready to build the container for our webfiles.

To update the fbsearch index, you need to delete it first and redo the process of creating and repopulating.
To delete the fbsearch index, access the fb-elastic container and issue the Xdelete command.
```
$ sudo docker exec -it fb-elastic bash 
curl -s -XDELETE "http://localhost:9200/fbsearch"

```


# FishBase v3.5
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

FishBase v3.5 is the old version of FishBase. To create the fishbasev3.5 container build the image first.

```
$ sudo docker compose build fishbasev3
```
Then create the container.
```
$ sudo docker compose up -d fishbasev3
```
Check if the container and Apache is running by checking the log file. 
```
$ sudo docker logs fb-webv3
```
The image below shows that Apache is is now running as a background process in the container. The issue about domain name shown below will be resolved in the proxy container.<br />
![apacheforeground](https://user-images.githubusercontent.com/34087729/193181380-1899aac8-3f54-4547-bde7-d6d580af0475.JPG)

### Clone FishBase
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

FishBase 3.5 has its own repository [here](https://github.com/FiN-FBSLB/FishBase3.5). Clone this repository inside the container by accessing the container first, cloning the repository and making sure that the document root points to the correct directory

```
$ sudo docker exec -it fb-webv3 bash
```
Inside the container do the following:
```
$ cd /var/www/html/
$ git clone https://github.com/FiN-FBSLB/FishBase3.5.git
Enter your username
Enter your personal access token

```
You may also need move the cloned files to the default Document root folder since cloning from git creates a new directory.
If you are still inside /var/www/html/ directory do the following:


```
$ mv /var/www/html/FishBase3.5/{.,}* .

```
Check if the contents of repository is now in /var/www/html/ instead of /var/www/html/FishBase3.5.

```
$ dir -al
```
You can now check if Fishbasev3.5 is working by issuing the command below while inside the container.

```
$ curl localhost/search.php
```
You should at least see an unrendered version of FishBase that looks something like the images below:

![v3a](https://user-images.githubusercontent.com/34087729/193185305-3b983f64-34b6-49c6-b896-17ccc3b90e3b.JPG)
![v3b](https://user-images.githubusercontent.com/34087729/193185318-c3b666e9-a524-40d0-9e18-edf1fe07ae92.JPG)

### Possible Errors
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

In cases that there will be a problem indicating failed connection to MySQL, check the following files below and make sure that it uses environmental variables. Please see image below for reference.

**includes/config.php** <br />
![config](https://user-images.githubusercontent.com/34087729/193185923-c9efc80a-75fd-4215-a8b3-f90ecce6f020.JPG)
<br /><br />
**includes/db.config.php** <br />
![config2](https://user-images.githubusercontent.com/34087729/193185952-5e96052e-6905-4e35-98e8-574c68c3a84a.JPG)

# FishBase v4
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

Fishbase v4 is the new look of Fishbase. It uses both fb-mysql and fb-elastic so you need to create those two first before proceeding.

Build the image by using the build command.

```
$ sudo docker compose build fishbasev4
```
![v4build](https://user-images.githubusercontent.com/34087729/193241427-770e705d-00a3-408e-ab23-48e2bed0b743.JPG)

Run the container by the command below. It should indicate that the containers fb-mysql and fb-elastic are running. 

```
$ docker compose up -d fishbasev4
```
![v4start](https://user-images.githubusercontent.com/34087729/193241682-b15be33d-d6a4-428a-b61b-ce41f39854fe.JPG)

### Clone FishBasev4
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

Access the container and clone the fishbasev4 repository. Make sure your current location is in /var/www/html and you have sufficient permission to do this processes.

```
$ sudo docker exec -it fb-webv4 bash
```
Inside the container do the following:
```
$ cd /var/www/html/
$ clone git https://github.com/FiN-FBSLB/FishBase4.0.git
Enter your username
Enter your personal access token

```
The same as the version 3 above, you need to copy the contents of the repository with in /var/www/html/
```
$ mv /var/www/html/FishBase4.0/{.,}* .

```
### Update Libraries
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

Install required libraries via composer. Do this process inside the container.

```
composer update
```
The image below shows the libraries used in fb-webv4 being updated.

![v4compose](https://user-images.githubusercontent.com/34087729/193243890-955491c8-80d5-4221-8330-85d573c1cd3c.JPG)


### Update env
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

Update the .env file by copying the .env.example to .env and then run artisan to generate an app_key

```
cp .env.example .env
php artisan key:generate
```
After the process above, you may need to exit the container to edit the .env file of fb-webv4. Press 'ctrl pq' to exit a container.

Open the .env file and check its contents. There should already be an entry in the APP_KEY variable as we have generated it earlier.
```
$ sudo vim .env
```
Fill up the remaining items. If you have copied the .env.example into .env you will only need to fill up the DB_USERNAME and DB_PASSWORD. Please make sure that the entries for this .env should match with the env in fb_slb_serverbuild/.env. Entries for both database connection should be the same. 

![databasesenv](https://user-images.githubusercontent.com/34087729/193250171-8a169b8a-b285-42d3-8164-2b481f2b34c4.JPG)

You can check if the webv4 is working well by accessing the container and issuing a curl command.
```
$ sudo docker exec -it fb-webv4 bash
curl localhost
```
It should again show an unrendered version of the fishbase version 4 website. It should look something like the image below.

![v4test](https://user-images.githubusercontent.com/34087729/193251626-79788953-d7ce-4ae8-b2f2-90ad1296b193.JPG)


# Proxy
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

Proxy is the last service declared in our docker-compose.yml. If all the other container are up and running and the entries in .env file in fb_slb_serverbuild. directory are properly filled up, you only need to build and up this service and the server should be already viewable.

```
$ sudo docker compose build proxy
$ sudo docker compose up -d proxy
```
If you have a registered domain name, you can now check the website online.

# Assets
[Back to top](https://github.com/FiN-FBSLB/fb_slb_serverbuild#table-of-contents)

Assets folder contains all the image files for fishbasev3. For the August 2022 mirror update, assets folder was archived and uploaded to dropbox.
To donwload from dropbox use the command below:

```
$ sudo curl -L -o asset.7z https://www.dropbox.com/sh/smlsnlp8ub536e8/AAAMrIdSlQ7DG442bRKbzopZa?dl=1
```
Extract this file into the htdocs or in root FB directory.



