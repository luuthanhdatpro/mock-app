def registry = 'https://thanhlc3.jfrog.io'
def imageName = 'thanhlc3.jfrog.io/thanhlc3-docker-local/ttrend'
def version   = '2.1.2'
pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    
    environment {
        PATH = "/opt/apache-maven-3.9.4/bin:$PATH"
    }

    stages {
        stage('build') {
            steps {
                echo "--- build started ----"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "--- build ended ----"
            }
        }
        stage("test") {
            steps {
                echo "--- unit test started ----"
                sh 'mvn surefire-report:report'
                echo "--- unit test ended ----"
            }
        }
        // stage('Sonar analysis') {
        //     environment {
        //         scannerHome = tool 'sonar-scanner'
        //     }
        //     steps{
        //         withSonarQubeEnv('sonarqube-server') {
        //             sh "${scannerHome}/bin/sonar-scanner"
        //         }
        //     }
        // }

    stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artifactory-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended -------------->'  
            }
        }   
    }

    stage(" Docker Build ") {
      steps {
        script {
           echo '<--------------- Docker Build Started --------------->'
           app = docker.build(imageName+":"+version)
           echo '<--------------- Docker Build Ends --------------->'
        }
      }
    }

    stage (" Docker Publish "){
        steps {
            script {
               echo '<--------------- Docker Publish Started --------------->'  
                docker.withRegistry(registry, 'artifactory-cred'){
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
            }
        }
    }

    // stage(" Deploy ") {
    //    steps {
    //      script {
    //         sh './deploy.sh'
    //      }
    //    }
    //  }
    }
}