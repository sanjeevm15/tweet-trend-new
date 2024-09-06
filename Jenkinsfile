pipeline {
    agent {
        node {
            label 'maven'
        }
    }

    tools {
        maven 'Maven 3.9.9'  // This is the name you provided for the Maven installation
    }

environment {
    PATH = "/opt/apache-maven-3.9.2/bin:$PATH"
}
    stages {
        stage("build"){
            steps {
                 echo "----------- build started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                 echo "----------- build complted ----------"
            }
        }
     }
   } 


