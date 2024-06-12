def frontendImage = "miner111/pandacore/frontend_v1"
def backendImage = "miner111/pandacore/backend_v1"

pipeline {
    agent {
        label 'agent'
    }
    parameters {
        string defaultValue: 'latest', name: 'backendDockerTag'
        string defaultValue: 'latest', name: 'frontendDockerTag'            
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

    }
}
