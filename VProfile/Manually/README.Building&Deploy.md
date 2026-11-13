# Build and Deploy VProfile

Run these steps on `app01`, where Tomcat is installed. This guide downloads Maven, builds the application, and deploys the WAR file to Tomcat.

## Download Maven

Download the Maven ZIP archive on `app01`:

```bash
cd /tmp
wget https://archive.apache.org/dist/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.zip
```
Limit Maven's memory usage on this VM:

```bash
export MAVEN_OPTS="-Xmx512m"
```

## Download the application source

The following command clones the `local` branch:
```bash
git clone -b local https://github.com/hkhcoder/vprofile-project.git
```
## Configure the application

Open the application's `application.properties` file:

```bash
cd vprofile-project
vim src/main/resources/application.properties
Update file with backend server details # Keep in eye the content below.
```

Update the connection settings to match the services configured in the other VMs. 

Pay particular attention to the database host, database name, username, and password. Replace example credentials with values for your lab, and do not commit real passwords.

```ruby
#JDBC Configutation for Database Connection
jdbc.driverClassName=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://db01:3306/accounts?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull


# Here is the username and passwor that you created and stablished for msql
jdbc.username=changeActualUsername
jdbc.password=changeActualPassword

#Memcached Configuration For Active and StandBy Host
#For Active Host
memcached.active.host=mc01
memcached.active.port=11211
#For StandBy Host
memcached.standBy.host=127.0.0.2
memcached.standBy.port=11211

#RabbitMq Configuration
rabbitmq.address=rmq01
rabbitmq.port=5672
rabbitmq.username=test
rabbitmq.password=test

#Elasticesearch Configuration
elasticsearch.host=localhost
elasticsearch.port=9300
elasticsearch.cluster=vprofile
elasticsearch.node=vprofilenode


spring.servlet.multipart.max-file-size=128KB
spring.servlet.multipart.max-request-size=128KB

logging.level.org.springframework.security=DEBUG


# application.properties
spring.security.user.name=admin_vp
spring.security.user.password=admin_vp
spring.security.user.roles=ADMIN


spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp

#logging.level.root=OFF
#logging.level.org.springframework.web=OFF
#spring.main.banner-mode=OFF

# Hibernate SQL Queries
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.format_sql=false
logging.level.org.hibernate.SQL=OFF
logging.level.org.hibernate.type=OFF


# Debug Logging for SecurityServiceImpl
logging.level.com.visualpathit.account.service.SecurityServiceImpl=OFF

```
## Build the application

Run the following commands from the application repository. The build produces `target/vprofile-v2.war`:

```bash
/usr/local/maven3.9/bin/mvn install
# Output check if we have this files downloaded and find de vprofile-v2.war
ls -l
cd target
ls 
vprofile-v2.war
```
## Deploy the application

Stop Tomcat before replacing the current deployment:
```bash
systemctl stop tomcat
```
Tomcat serves the application from its `ROOT` deployment. The following commands remove the existing `ROOT` deployment, copy the new WAR into place, and restart Tomcat:
```bash
 rm -rf /usr/local/tomcat/webapps/ROOT*
 cp target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
 systemctl start tomcat
 chown tomcat.tomcat /usr/local/tomcat/webapps -R
 systemctl restart tomcat
```

Return to the [manual setup overview](README.md).