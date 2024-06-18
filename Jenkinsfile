pipeline {
    agent any
    stages {
        stage('Source') {
            steps {
                git 'https://github.com/stancodess/unir-cicd'
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
                archiveArtifacts artifacts: 'results/*'
            }
        }
        stage('API tests') {
            steps {
                echo 'Running API tests!'
                sh '''
                docker network create calc-test-api || true
                docker run -d --network calc-test-api --env PYTHONPATH=/opt/calc --name apiserver --env FLASK_APP=app/api.py -p 5000:5000 -w /opt/calc calculator-app:latest flask run --host=0.0.0.0
                docker run --network calc-test-api --name api-tests --env PYTHONPATH=/opt/calc --env BASE_URL=http://apiserver:5000/ -w /opt/calc calculator-app:latest pytest --junit-xml=results/api_result.xml -m api || true
                docker cp api-tests:/opt/calc/results ./api-results
                docker stop apiserver || true
                docker rm --force apiserver || true
                docker stop api-tests || true
                docker rm --force api-tests || true
                docker network rm calc-test-api || true
                '''
                archiveArtifacts artifacts: 'api-results/*'
            }
        }
        stage('E2E tests') {
            steps {
                echo 'Running E2E tests!'
                sh 'make test-e2e'
                archiveArtifacts artifacts: 'e2e-results/*'
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
                //mail to: 'stalin.caiche@gmail.com',
                //       subject: "Build failed: ${jobName} #${buildNumber}",
                //       body: "El trabajo ${jobName} con número de ejecución ${buildNumber} ha fallado."
            }
        }
    }
}
