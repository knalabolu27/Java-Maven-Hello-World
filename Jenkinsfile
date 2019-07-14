pipeline {
    agent any
    tools {
        maven 'Maven-3.3.3'
        jdk 'JDK1.7'
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
            }
        }
        /* stage ('Checkout') {
            steps {
                git poll: false, url: 'https://github.com/Preethireddy95/Java-Maven-Hello-World.git'
            }
        } */
        stage ('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage ('Deploy') {
            steps {
                sh '''
                    sudo /apache-tomcat-9.0.22/bin/shutdown.sh
                    sudo rm -rf /apache-tomcat-9.0.22/webapps/hello* /apache-tomcat-9.0.22/logs/*
                    sudo cp target/*.war /apache-tomcat-9.0.22/webapps/hello-world.war
                    sudo /apache-tomcat-9.0.22/bin/startup.sh
                '''
            }
        }
    }
}
