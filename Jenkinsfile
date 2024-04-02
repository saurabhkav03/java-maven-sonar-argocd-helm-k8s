pipeline {
    agent {
        docker { image 'abhishekf5/maven-abhishek-docker-agent:v1' }
        args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // The Docker container is configured to have root access (--user root) and mount the Docker socket from the host machine (-v /var/run/docker.sock:/var/run/docker.sock). This setup allows the Jenkins job to perform Docker-related operations, such as building Docker images or running containers.

    }

    stages {
        stage('Checkout') {
            steps {
                sh 'echo Source code is already added in git'
                //git([url: 'https://github.com/saurabhkav03/Jenkins.git', branch: 'main'])
            }
        }
        stage('Build') {
            steps {
                
            }
        }
        stage('Static Code Analysis') {
            environment {
                SONAR_URL = ''
            }
            steps {
                withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')])
                sh 'cd java-maven-sonar-argocd-helm-k8s/spring_boot_app && mvn clean package && mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
            }
        }

        stage('docker image') {
            environment {
                DOCKER_IMAGE = ''
                REGISTRY_CREDENTIALS = credentials('docker-cred')
            }
            steps {
                script {
                    sh 'cd java-maven-sonar-argocd-helm-k8s/spring_boot_app && docker build -t ${DOCKER_IMAGE} .'
                    def docker_image = docker.image('${DOCKER_IMAGE}')
                    docker.withRegistry('https://registry.example.com', '${REGISTRY_CREDENTIALS}') {
                        docker_image.push()
                    }
                }
            }
        }

        stage('Update Deployment file') {
            environment {
                GIT_REPO_NAME = 'Jenkins'
                GIT_USER_NAME = ''
            }
        steps {
            //This block is used to securely access the GitHub repository using credentials stored in Jenkins.
            //This line retrieves the GitHub token from Jenkins credentials with the ID "github" and assigns it to the environment variable GITHUB_TOKEN.
            withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                sh '''
                
                    git config user.email "abhishek.xyz@gmail.com"
                    git config user.name "Abhishek Veeramalla"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/deployment.yml
                    git add java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/deployment.yml
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                '''


        }
    }
}