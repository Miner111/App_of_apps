def frontendImage = "miner111/pandacore"
def backendImage = "miner111/pandacore"

pipeline {
    agent {
        label 'agent'
    }
    parameters {
        string defaultValue: 'backend_v1', name: 'backendDockerTag'
        string defaultValue: 'frontend_v1', name: 'frontendDockerTag'            
    }
    environment {
        PIP_BREAK_SYSTEM_PACKAGES = 1
    }
    stages {
        stage('pobierz kod') {
            steps {
                checkout scm
            }
        }
        stage('wskaznik wersji') {
            steps {
                script{
                    currentBuild.description = "Backend: ${backendDockerTag}, Frontend: ${frontendDockerTag}"
                }
            }
        }
        stage("sprzątanie kontenerów") {
            steps {
                sh 'docker rm -f frontend backend'
            }
        }
        stage('Deploy application') {
            steps {
                script {
                    withEnv(["FRONTEND_IMAGE=$frontendImage:$frontendDockerTag", 
                             "BACKEND_IMAGE=$backendImage:$backendDockerTag"]) {
                            sh "docker-compose up -d"
                    }
                }
            }
        }
        stage('Testy selenium') {
            steps {
                sh "pip3 install -r test/selenium/requirements.txt"
                sh "python3 -m pytest test/selenium/frontendTest.py"
            }
        }
    }
    post {
        always {
            sh "docker compose down"
            cleanWs()
        }
    }
}
