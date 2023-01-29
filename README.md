<p align="center"><img src="thumbnail.svg" alt="Thumbnail" style="zoom:25%;" /></p>

# HealthIT Uonafya Projects

This monorepo consists of repositories and forks of the HealthIt project I am working on.

This projects help solve and improve health services across africa. You can learn and make an impact by contributing to this projects.

# Setup for Monorepo

The monorepo consists of submodules so a clone of the project is not enough. You have to initialize the respective repositories {Clone the various repositories}.

To initialize the monorepo, run the following

```bash
git submodule update --init
```

This clones and does a checkout on the respective submodules.

## KenyaEMR
### Setup for docker containers
To build a module you have to build the base docker compose file.

```bash
docker compose up
```

### Setup on local machines or VM
To setup one has to install the prequisites, this guide assumes that you are using linux with the `debian` distro.

#### Installing requirements
You have to have `jdk[7|8] + mysql[5]` installed to get the system started. You can install it by running the following

```bash
# Update system repositories
$ sudo apt-get update

# Install JDK 8 
$ sudo apt install openjdk-8-jdk

# Add system repositories for mysql 5
$ sudo add-apt-repository 'deb http://archive.ubuntu.com/ubuntu trusty universe'
$ sudo add-apt-repository 'deb http://kr.archive.ubuntu.com/ubuntu xenial main'

# Update system repositories
$ sudo apt-get update

# Install mysql 5 
$ sudo apt-get install mysql-server-5.6
```

#### Setting up tomcat + Openmrs
After setting up requirements you have to install tomcat server and setup Openmrs

```bash
# Install tomcat 9
$ sudo apt install tomcat9 tomcat9-docs tomcat9-examples tomcat9-admin

# Update system repositories
$ sudo apt-get update
```

Optimize tomcat for Openmrs

```bash
# Edit upload and download status
$ sudo nano /usr/share/tomcat9-admin/manager/WEB-INF/web.xml

# Edit this lines 
# -----------------------------------------------------
# - <max-file-size>209715200</max-file-size>          -
# - <max-request-size>209715200</max-request-size>    -
# -----------------------------------------------------

# Optimize routing
$ sudo nano /var/lib/tomcat9/conf/catalina.properties

# Edit the lines
# ----------------------------------------------------------------
# - org.apache.tomcat.util.buf.UDecoder.ALLOW_ENCODED_SLASH=true -
# ----------------------------------------------------------------

# Add administation users
$ sudo nano /etc/tomcat9/tomcat9-users.xml

# Edit the lines
# ----------------------------------------------------------------------------------------
# - <role rolename=”tomcat”/>                                                            -
# - <role rolename=”manager-gui”/>                                                       -
# - <role rolename=”admin-gui”/>                                                         -
# - <user username=”admin” password=”PASSWORD” roles=” tomcat, manager-gui, admin-gui”/> -
# ----------------------------------------------------------------------------------------

# Optimize runtime for tomcat6
$ sudo nano /etc/default/tomcat9

# Edit the lines
# --------------------------------------------------------------------------------------------------------
# - JAVA_OPTS="${JAVA_OPTS} -Xmx2048m -Xms1024m -XX:PermSize=512m -XX:MaxPermSize=512m -XX:NewSize=256m" -
# --------------------------------------------------------------------------------------------------------

# Create Openmrs configs
$ sudo mkdir /usr/share/tomcat6/.OpenMRS
$ sudo chown -R tomcat:tomcat /usr/share/tomcat6/.OpenMRS
$ sudo chmod 755 -R /usr/share/tomcat6/.OpenMRS


# Add openmrs and its modules
$ sudo cp kenyaemr-build/openmrs.war /var/lib/tomcat/webapps/
$ sudo cp -R kenyaemr-build/modules/ /usr/share/tomcat6/.OpenMRS/

# Change the ownership of modules
sudo chown tomcat6:tomcat6 /usr/share/tomcat6/.OpenMRS/*.omod
sudo chmod +r /usr/share/tomcat6/.OpenMRS/*.omod

# [ ⚠️ Optional and not recommended unless you know what you are doing] 
# Redirect logs to catalina : Run the command
$ sudo nano /lib/systemd/system/tomcat9.service

# Then comment out the line:
# -----------------------------------------------
# - SyslogIdentifier=tomcat9                    -
# -----------------------------------------------

# Once done, in the same file, add this 2 lines: just below the line commented out
# --------------------------------------------------------
# - StandardOutput=append:/var/log/tomcat9/catalina.out  -
# - StandardError=append:/var/log/tomcat9/catalina.out   -
# --------------------------------------------------------

# Under Service still in same file, add this below it and save the file
# --------------------------------------------
# - ReadWritePaths=/var/lib/OpenMRS/         -
# --------------------------------------------

# Start Tomcat
$ sudo systemctl start tomcat9
```

#### Setting up Mysql for Kenyaemr
To prevent concept dictionary error upwords in setting up opemrs, you have to setup mysql with the blank openmrs db.

```bash
# You first have to download the blank db
$ curl -o /home/$USER/blank_openmrs.sql https://doc-04-0s-docs.googleusercontent.com/docs/securesc/jf7akb1jamo3c8fe0bq8nj8o453kqbf7/biqdab50u1v7rh9a0om7nqndv5f8js8p/1674889875000/01074820708144119444/06645273130455365593Z/14rlO3sG9yTL9igAN8xbVqKfctyuemDN8?e=download&uuid=722cca23-3cd9-46ef-a625-857941c3b0aa&nonce=kq281t3gq5l5q&user=06645273130455365593Z&hash=7cnetb52tdt3ma2pmvnj3lk0su7tlbcm

# When the download is done, login into mysql
$ mysql -u root -p

```

```sql
-- Switch to openmrs and source the db
use openmrs;

source /home/$USER/blank_openmrs.sql; 

-- Grant privileges to openmrs user
grant privileges on *.* to ‘openmrs_user’@’localhost’; 

-- delete liquidbase changelog
delete from liquibasechangelog where id like ‘kenyaemrChart%’;

-- `exit` from mysql
```

Restart tomcat to refresh the db
```bash
$ sudo systemctl restart tomcat9
```