pipeline {
    agent any

    environment {
        DOCKER_IMAGE  = "vishal1326/cicd-demo"
        SONAR_PROJECT = "Vishal5205_cicd-demo"
        SONAR_ORG     = "vishal5205"
        IMAGE_TAG     = "${BUILD_NUMBER}"
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
                        sonar-scanner \
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

        stage('Docker Build') {
            steps {
                sh """
                docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} .
                docker tag ${DOCKER_IMAGE}:${IMAGE_TAG} ${DOCKER_IMAGE}:latest
                """
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS')
                ]) {
                    sh """
                    echo \$PASS | docker login -u \$USER --password-stdin
                    docker push ${DOCKER_IMAGE}:${IMAGE_TAG}
                    docker push ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }

        stage('Update K8s Manifest (GitOps)') {
            steps {
                sh """
                sed -i 's|image:.*|image: ${DOCKER_IMAGE}:${IMAGE_TAG}|' k8s/deployment.yaml
                git config user.email "jenkins@local"
                git config user.name "jenkins"
                git add k8s/deployment.yaml
                git commit -m "Update image to ${IMAGE_TAG}"
                git push origin main
                """
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully"
        }
        failure {
            echo "Pipeline failed"
        }
    }
}
