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
    }
}