# Build and Deploy Java Maven HelloWorld Application in Tomcat 9 with JDK 8 in Amazon EC2

## Launch an instance
Select amazon linux instance and configure the settings. In the step-5 Add tags with the name and in the step-6 configure security group with a new security group(open port 22 in the inbound rule with CIDR 0.0.0.0/0 to access from anywhere for now).
A key pair gets downloaded before launching the instance
### Change the read only permissions to the pem file
`chmod 400 Work.pem`
### Connect to the instance from the command line
`ssh -i "<path of the pem file>" ec2-user@ec2-36-215-39-147.us-west-2.compute.amazonaws.com`

## Update all the applications before installing
`sudo yum update -y`

## Install java
https://bhargavamin.com/how-to-do/setting-up-java-environment-variable-on-ec2/
```
sudo yum install java-1.8.0
sudo yum remove java-1.7.0-openjdk
```
### To check the java installed location
`file $(which java)`
### To set the java path
```
sudo /usr/sbin/alternatives --set java /usr/lib/jvm/jre-1.8.0-openjdk.x86_64/bin/java
sudo /usr/sbin/alternatives --set javac /usr/lib/jvm/jre-1.8.0-openjdk.x86_64/bin/javac
export JAVA_HOME="/usr/lib/jvm/jre-1.8.0-openjdk.x86_64"
export PATH=$JAVA_HOME/bin:$PATH  
```
### To check JAVA_HOME
`echo $JAVA_HOME`

## Install Maven
http://www.aodba.com/how-to-install-apache-maven-on-amazon-linux/
```
sudo wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo
sudo sed -i s/\$releasever/6/g /etc/yum.repos.d/epel-apache-maven.repo
sudo yum install -y apache-maven
mvn --version
```

## Install Git
### Install Git using yum
```
sudo yum install git -y
git version
```

## Clone Java program and Build with Maven
### Creating a directory
```
mkdir mygit
cd mygit/
```
### To check the status (of working tree)
`git status`
### Clone the hello world program from github
`git clone https://github.com/Preethireddy95/Java-Maven-Hello-World.git`
### Change the directory in to Java-Maven-Hello-World
`cd Java-Maven-Hello-World/`
### Build the application
`mvn clean install`
After a success build, check the target directory to find an artifact(war file)

## Install Tomcat and deploy application in tomcat
https://devops4solutions.com/installation-of-tomcat-on-aws-ec2-linux-integration-with-jenkins/
### Create a directory temp for tomcat downloading
```
mkdir temp
cd temp
```
### To download tomcat binary
`wget http://apache.mirrors.pair.com/tomcat/tomcat-9/v9.0.22/bin/apache-tomcat-9.0.22.tar.gz`
### Unzip the tar file
`tar -zvxf apache-tomcat-9.0.22.tar.gz`
### Copy the war file  to webapps directory
`cp <path of the war file> <path of the webapps directory>`
### Start the tomcat server from bin directory
`./startup.sh`
### To check the logs of tomcat
`tail -f ../logs/catalina.out`
### To validate the application
```
curl localhost:8080/hello-world
http://<serverIP>:8080/hello-world
```
### Stop the tomcat server
`./shutdown.sh`

## Access the tomcat manager
### From same host
Add following content to conf/tomcat-users.xml.
```
<role rolename="manager-gui"/>
<user username="tomcat" password="s3cret" roles="manager-gui"/>
```
### From Different host
If we could not open manager app on the server then , you need to create a file conf/Catalina/localhost/manager.xml and specify the rule you want to allow remote access. For example, the following content of manager.xml will allow access from all machines:
https://stackoverflow.com/questions/36703856/access-tomcat-manager-app-from-different-host
```
<Context privileged="true" antiResourceLocking="false"
         docBase="${catalina.home}/webapps/manager">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" />
</Context>
```
