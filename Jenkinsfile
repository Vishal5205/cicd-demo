pipeline {
    agent any

    environment {
        DOCKER_IMAGE  = "vishal1326/cicd-demo"
        SONAR_PROJECT = "Vishal5205_cicd-demo"
        SONAR_ORG     = "vishal5205"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/Vishal5205/cicd-demo.git'
            }
        }

        stage('SonarCloud Scan') {
            steps {
                withSonarQubeEnv('SonarCloud') {
                    withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                        sh """
                        /opt/sonar-scanner/bin/sonar-scanner \
                        -Dsonar.projectKey=${SONAR_PROJECT} \
                        -Dsonar.organization=${SONAR_ORG} \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=https://sonarcloud.io \
                        -Dsonar.token=\$SONAR_TOKEN
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 15, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:latest ."
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {
                    sh """
                    echo \$PASS | docker login -u \$USER --password-stdin
                    docker push ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                """
            }
        }
    }

    post {
        success {
            echo "CI/CD Pipeline Completed Successfully"
        }
        failure {
            echo "Pipeline Failed"
        }
    }
}
