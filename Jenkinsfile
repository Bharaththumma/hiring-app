pipeline {
    agent any

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Docker Build') {
            steps {
                script {
                    docker.build("bharaththumma/hiring-app:${BUILD_NUMBER}")
                }
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-jenkins', variable: 'hubPwd')]) {
                    script {
                        docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-jenkins') {
                            docker.image("bharaththumma/hiring-app:${BUILD_NUMBER}").push()
                        }
                    }
                }
            }
        }

        stage('Checkout K8S manifest SCM') {
            steps {
                script {
                    git branch: 'main', credentialsId: 'GIT_PASSWORD', url: 'https://github.com/Bharaththumma/Hiring-app-argocd.git'
                }
            }
        }

        stage('Update K8S manifest & push to Repo') {
            steps {
                script {
                    git config user.email "bharatht95@outlook.com"
                    git config user.name "Bharaththumma"
                    sh "sed -i 's/3/${BUILD_NUMBER}/g' /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml"
                    git add 'dev/deployment.yaml'
                    git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                    git push 'https://$GIT_TOKEN@github.com/Bharaththumma/Hiring-app-argocd1.git' main
                }
            }
        }
    }
}
