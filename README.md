# Install Tomcat Webserver for running Java Application on Amazon Linux

### We will be doing these following steps -
Steps to install and configure Tomcat
Install Java on Machine
Install Tomcat on Machine
Add Execute Permission to startup.sh & shutdown.sh
Create link files for Tomcat Server up and Down
Change Settings to Manage Tomcat
Update user information in tomcat-users.xml

### War file generation
Java installation along with maven app installation are been done in the following repo -
#### https://github.com/ajinkyadawange31045/maven-setup-for-AmazonLinux-ec2

#### Let's start with Tomcat
Enter sudo 
```bash
sudo su -
```

enter the opt directory 
```bash
cd /opt
```

Download tomcat binary, copy the tar file from the above link https://tomcat.apache.org/download-90.cgi
```bash
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.82/bin/apache-tomcat-9.0.82.tar.gz
```

It generates the following file
`apache-tomcat-9.0.82.tar.gz`

unzip tomcat binary
```bash
tar -zvxf apache-tomcat-9.0.82.tar.gz
```

When you check using `ls`, you will see that 2 files are been created
`apache-tomcat-9.0.82  apache-tomcat-9.0.82.tar.gz  aws`

Add Execute Permission to startup.sh & shutdown.sh
```bash
cd apache-tomcat-9.0.82/
cd bin
```

##### Then give excecution permission to the startup.sh and shutdown.sh
```bash
chmod +x startup.sh
chmod +x shutdown.sh
```

##### Change Settings to Manage Tomcat
come out of bin and edit the context.xml files as mentioned below -
```bash
cd ..
```

After using find command `find -name context.xml`, you will notice the following files.
`
./webapps/docs/META-INF/context.xml
./webapps/examples/META-INF/context.xml
./webapps/host-manager/META-INF/context.xml
./webapps/manager/META-INF/context.xml
`

Change <Valve> section like below (allow attribute value changed to `allow = ".*"`)
or
Comment value tag sections in below all files
```bash
vi ./webapps/examples/META-INF/context.xml
vi ./webapps/host-manager/META-INF/context.xml
vi ./webapps/manager/META-INF/context.xml
```

Check this example, of how the allow attribute is been changed to `".*"` from `"127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1"`
```bash
<Context antiResourceLocking="false" privileged="true" >
     <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow=".*" />
</Context>
```


##### Update user information in tomcat-users.xml
Add tomact users in "tomact-folder/conf/tomact-users.xml" file like below 
```bash
vi conf/tomcat-user.xml
```

add the following command
```bash
<role rolename="manager-gui" />
<user username="tomcat" password="tomcat" roles="manager-gui" />
<role rolename="admin-gui" />
<user username="admin" password="admin" roles="manager-gui,admin-gui"/>
```
`esc` + `:wq`+`enter`

Stop the tomact server and start it
```bash
cd bin
./shutdown.sh
./startup.sh
```



### Steps to display Maven Web Application in Tomcat Server
As we are now here as root user, so we will transfer the file which we were having in home directory which we have created in the maven repo, to webapps of our apache Application. (which is in /opt/apache-)
```bash
cd /home/ec2-user/My-first-webapp/target/
mvn clean package
```
copy the packaged war file and transfer it to Tomcat webapps directory.
```bash
cp My-first-webapp.war /opt/apache-tomact-9.0.82
```

Now stop and start the server again
```bash
cd /opt/apache-tomact-9.0.82/bin
./shutdown.sh
./startup.sh
```

By default the tomcat server runs on port number 8080. So we need to update the security groups rules. Add custom tcp connection with port number 8080.

and you can check your Application at url structure similar to that given below
`http://<your-server-ip>:8080/`

Id and password will be `admin`.

In that click on Manage App, and in application we will have our My-first-webapp, click on that and you will see the webapp being hosted.





