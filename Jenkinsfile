def registry = 'https://sanjeev14.jfrog.io'
def imageName = 'valaxy05.jfrog.io/demorepo-docker-local/ttrend'
def version = '2.1.2'
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

	 stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"Jfrogartifact-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "maven-libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'

            }
        }
    }

    stage(" Docker Build ") {
    steps {
     script {
         echo '<- Docker Build Started -->'
         app = docker.build(imageName+": "+version)
          echo '<-Docker Build Ends ->'
     } 
    }
    }
 
     stage ("Docker Publish "){
      steps {
      script {
       echo '<- Docker Publish Started ->'
       docker.withRegistry (registry, 'artifactory_token') {
       app.push()
       echo '<- Docker Publish Ended ->'
      }
     }
    }
   }      
 }
}