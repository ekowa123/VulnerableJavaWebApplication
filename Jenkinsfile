pipeline {
    agent none
    stages {
        stage('Maven Compile'){
            agent {
               label 'maven'
            }
            steps {
                sh 'mvn compile spotbugs:spotbugs'
				archiveArtifacts artifacts: 'target/spotbugs.html'
				archiveArtifacts artifacts: 'target/spotbugs.xml'
            }
        }
		stage('Secret Scanning') {
			agent {
				docker {
					image 'trufflesecurity/trufflehog:latest'
					args '-v /var/run/docker.sock:/var/run/docker.sock --entrypoint='
				}
			}
			steps {
				sh 'trufflehog --no-update filesystem . --json > trufflehogscan.json'
				sh 'cat trufflehogscan.json'
				archiveArtifacts artifacts: 'trufflehogscan.json'
			}
		}
/*		stage('SCA') {
			agent {
				docker {
					image 'owasp/dependency-check:latest'
					args '-v /var/run/docker.sock:/var/run/docker.sock --entrypoint='
				}
			}
			steps {
				sh '/usr/share/dependency-check/bin/dependency-check.sh --scan . --project "VulnerableJavaWebApplication" --format ALL'
				archiveArtifacts artifacts: 'dependency-check-report.html'
				archiveArtifacts artifacts: 'dependency-check-report.json'
				archiveArtifacts artifacts: 'dependency-check-report.xml'
			}
		} */
        stage('building image docker') {
            agent {
                docker {
                    image 'docker:dind'
                    args '-u root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
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