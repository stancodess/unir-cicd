pipeline {
    agent any
    stages {
        stage('Source') {
            steps {
                git 'https://github.com/tu-usuario/unir-cicd.git'
            }
        }
        stage('Build') {
            steps {
                echo 'Building stage!'
                sh 'make build'
            }
        }
        stage('Unit tests') {
            steps {
                sh 'make test-unit'
                archiveArtifacts artifacts: 'results/*.xml'
            }
        }
        stage('API tests') {
            steps {
                echo 'Running API tests!'
                sh 'make test-api'
                archiveArtifacts artifacts: 'api-results/*.xml'
            }
        }
        stage('E2E tests') {
            steps {
                echo 'Running E2E tests!'
                sh 'make test-e2e'
                archiveArtifacts artifacts: 'e2e-results/*.xml'
            }
        }
    }
    post {
        always {
            junit 'results/*_result.xml'
            junit 'api-results/*_result.xml'
            junit 'e2e-results/*_result.xml'
            cleanWs()
        }
        failure {
            script {
                def jobName = env.JOB_NAME
                def buildNumber = env.BUILD_NUMBER
                echo "El trabajo ${jobName} con número de ejecución ${buildNumber} ha fallado."
                mail to: 'stalin.caiche@gmail.com',
                      subject: "Build failed: ${jobName} #${buildNumber}",
                      body: "El trabajo ${jobName} con número de ejecución ${buildNumber} ha fallado."
            }
        }
    }
}
