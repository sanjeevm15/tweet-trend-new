def registry = 'https://sanjeev14.jfrog.io'
def imageName = 'sanjeev14.jfrog.io/demorepo-docker-local/ttrend'
def version = '2.1.2'

pipeline {
    agent {
        node {
            label 'maven'
        }
    }

    tools {
        maven 'Maven 3.9.9'  // Ensure this name matches the Maven installation in Jenkins
    }

    environment {
        PATH = "/opt/apache-maven-3.9.2/bin:$PATH"
    }

    stages {
        stage("Build") {
            steps {
                echo "----------- Build Started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------- Build Completed ----------"
            }
        }

        stage("Jar Publish") {
            steps {
                script {
                    echo '<--------------- Jar Publish Started --------------->'
                    def server = Artifactory.newServer url: registry + "/artifactory", credentialsId: "Jfrogartifact-cred"
                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "jarstaging/com/valaxy/demo-workshop/2.1.2/*",
                                "target": "maven-libs-release-local/com/valaxy/demo-workshop/2.1.2/",
                                "flat": false,
                                "props": "${properties}",
                                "exclusions": [ "*.sha1", "*.md5" ]
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

     stage("Docker Build") {
       steps {
         script {
            echo '<- Docker Build Started ->'
            // Build the Docker image and assign it to the 'app' variable
            app = docker.build(imageName + ":" + version)
            echo '<- Docker Build Ended ->'
        }
    }
}

     stage("Docker Publish") {
       steps {
        script {
            echo '<- Docker Publish Started ->'
            // Use the 'app' variable to push the Docker image
            docker.withRegistry(registry, 'Jfrogartifact-cred') {
                app.push()
            }
            echo '<- Docker Publish Ended ->'
        }
    }
}



        stage("Deploy") {
            steps {
                script {
                    sh './deploy.sh'
                }
            }
        }
    }
}
