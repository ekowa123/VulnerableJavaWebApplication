pipeline {
    agent none
    stages {
        // stage('Maven Compile'){
        //     agent {
        //         label 'maven'
        //     }
        //     steps {
        //         git 'https://github.com/ekowa123/VulnerableJavaWebApplication'
        //         sh 'mvn compile'
        //     }
        // }
        stage('building image docker') {
            agent {
                docker {
                    image 'docker:dind'
                    args '-u root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
        //        git 'https://github.com/ekowa123/VulnerableJavaWebApplication'
                sh 'docker build -t vulnerable-java-aplication:0.1 .'
            }
        }
        stage('run docker image'){
            agent {
                label 'built-in'
            }
            steps {
                sh 'docker rm --force vulnerable-java-aplication'
                sh 'docker run --name vulnerable-java-aplication -p 9000:9000 -d vulnerable-java-aplication:0.1'
            }
        }
    }
}