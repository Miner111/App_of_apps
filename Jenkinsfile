def frontendImage = "miner111/pandacore"
def backendImage = "miner111/pandacore"

pipeline {
    agent {
        label 'agent'
    }
    tools {
        terraform 'Terraform'
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
        stage('Uruchom Terraform') {
            steps {
                dir('Terraform') {                
                    git branch: 'main', url: 'https://github.com/Miner111/Terraform.git'
                    withAWS(credentials:'AWS', region: 'us-east-1') {
                            sh 'terraform init -backend-config=bucket=jan-jasiewicz-panda-devops-core-18'
                            sh 'terraform apply -auto-approve -var bucket_name=jan-jasiewicz-panda-devops-core-18'
                            
                    } 
                }
            }
        }
        stage('Run Ansible') {
               steps {
                   script {
                        sh "pip3 install -r requirements.txt"
                        sh "ansible-galaxy install -r requirements.yml"
                        withEnv(["FRONTEND_IMAGE=$frontendImage:$frontendDockerTag", 
                                 "BACKEND_IMAGE=$backendImage:$backendDockerTag"]) {
                            ansiblePlaybook inventory: 'inventory', playbook: 'playbook.yml'
                        }
                }
            }
        }
    }
    post {
        always {
            sh "docker-compose down"
            cleanWs()
        }
    }
}
