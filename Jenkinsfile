pipeline {
    agent {
        label 'agent'
    }
    parameters {
        def frontendImage = "miner111/pandacore/frontend_v1"
        def backendImage = "miner111/pandacore/backend_v1"
    }
    stages {
        stage('pobierz kod') {
            steps {
                checkout scm
            }
        }
    }
}
