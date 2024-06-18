pipeline {
    agent any

    environment {
        DOCKER_CLI_EXPERIMENTAL = 'enabled'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/stancodess/unir-cicd'
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'make build'
                }
            }
        }

        stage('Unit tests') {
            steps {
                script {
                    sh 'make test-unit'
                }
                junit 'results/unit_result.xml'
                archiveArtifacts artifacts: 'results/**', allowEmptyArchive: true
            }
        }

        stage('API tests') {
            steps {
                script {
                    sh 'make test-api'
                }
                junit 'results/api_result.xml'
                archiveArtifacts artifacts: 'results/**', allowEmptyArchive: true
            }
        }

        stage('E2E tests') {
            steps {
                script {
                    sh 'make test-e2e'
                }
                archiveArtifacts artifacts: 'e2e-results/**', allowEmptyArchive: true
            }
        }
    }

    post {
       always {
            echo 'Cleaning up workspace...'
            sh 'make clean'
            echo 'El trabajo Proyecto-UNIR-CICD ha fallado.'
            echo 'El trabajo Proyecto-UNIR-CICD ha sido exitoso.'
            archiveArtifacts artifacts: 'results/**', allowEmptyArchive: true
            archiveArtifacts artifacts: 'e2e-results/**', allowEmptyArchive: true
            junit 'results/*.xml'
        }

        success {
            echo "El trabajo ${env.JOB_NAME} con número de ejecución ${env.BUILD_NUMBER} ha sido exitoso."
            mail to: 'stalin.caiche@gmail.com',
                 subject: "Éxito: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "El trabajo ${env.JOB_NAME} con número de ejecución ${env.BUILD_NUMBER} ha sido exitoso.\n\nRevisa los detalles en ${env.BUILD_URL}"
        }

        failure {
            echo "El trabajo ${env.JOB_NAME} con número de ejecución ${env.BUILD_NUMBER} ha fallado."
            mail to: 'stalin.caiche@gmail.com',
                 subject: "Error: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "El trabajo ${env.JOB_NAME} con número de ejecución ${env.BUILD_NUMBER} ha fallado.\n\nRevisa los detalles en ${env.BUILD_URL}"
        }

        unstable {
            echo "El trabajo ${env.JOB_NAME} con número de ejecución ${env.BUILD_NUMBER} está inestable."
            mail to: 'stalin.caiche@gmail.com',
                 subject: "Inestable: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "El trabajo ${env.JOB_NAME} con número de ejecución ${env.BUILD_NUMBER} está inestable.\n\nRevisa los detalles en ${env.BUILD_URL}"
        }

        changed {
            echo "El trabajo ${env.JOB_NAME} con número de ejecución ${env.BUILD_NUMBER} ha cambiado su estado."
            mail to: 'stalin.caiche@gmail.com',
                 subject: "Cambio de estado: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "El trabajo ${env.JOB_NAME} con número de ejecución ${env.BUILD_NUMBER} ha cambiado su estado.\n\nRevisa los detalles en ${env.BUILD_URL}"
        }
    }
}
