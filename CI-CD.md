# Continuous Integration and Continuous Delivery for Java application

## Pre-requisites
* JAVA JDK 1.8
* Git
* Maven
* Tomcat 9

## Connect to the instance from the command line
`ssh -i "<path of the pem file>" ec2-user@ec2-36-215-39-147.us-west-2.compute.amazonaws.com`

## Update all the applications before installing
`sudo yum update -y`

## Installation of Jenkins
https://medium.com/@itsmattburgess/installing-jenkins-on-amazon-linux-16aaa02c369c
```
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
sudo rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
sudo yum install jenkins
sudo service jenkins start
```
### Jenkins to automatically start when your instance is started
`sudo chkconfig --add jenkins`
### To access Jenkins server
Make sure port 8080 is open in security group inbound rules of that virtual machine.
`http://{ec2-public-dns}:8080`
### Jenkins Home directory
`/var/lib/jenkins/`

## Change the default port of Jenkins
### Modify the port number in /etc/sysconfig/jenkins
```
# Port Jenkins is listening on.
# Set to -1 to disable
#
JENKINS_PORT="8080"
```

## To check the Jenkins server logs
`/var/lib/jenkins/logs/`

## Create a Hello World Free-Style Jenkins job
  * Click on New item from main menu in the Jenkins console.
  * Enter the Name 'HelloWorld', Select Freestyle project and then click Ok.
  * Check GitHub project from general Configuration page and give the Project URL.
    `https://github.com/Preethireddy95/Java-Maven-Hello-World/`
  * Select Git in Source Code Management and give the Repository URL.
    `https://github.com/Preethireddy95/Java-Maven-Hello-World.git`
  * Select Credentials 'None' as it is a public repository.
  * In Build Environment check 'Delete Workspace before build starts'.
  * In Build, Add build step 'Invoke top-level Maven targets' and enter the goals.
    `clean install`
  * In Build, Add build step 'Execute shell' and Add command.
    ```
      sudo /apache-tomcat-9.0.22/bin/shutdown.sh
      sudo rm -rf /apache-tomcat-9.0.22/webapps/hello* /apache-tomcat-9.0.22/logs/*
      sudo cp target/*.war /apache-tomcat-9.0.22/webapps/hello-world.war
      sudo /apache-tomcat-9.0.22/bin/startup.sh
    ```
  * Click Save.

## Build the Jenkins job
  * Click on 'Build now' button on the Jenkins job console.

## Accessing the Build logs
  * Click on 'console output' with the respective Build.
