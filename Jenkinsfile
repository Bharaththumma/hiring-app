pipeline {
    agent any

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Docker Build') {
            steps {
                script {
                    docker.build("bharaththumma/hiring-app:${IMAGE_TAG}")
                }
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-jenkins', variable: 'hubPwd')]) {
                    script {
                        docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-jenkins') {
                            docker.image("bharaththumma/hiring-app:${IMAGE_TAG}").push("${IMAGE_TAG}")
                        }
                    }
                }
            }
        }

        stage('Checkout K8S manifest SCM') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/Bharaththumma/Hiring-app-argocd.git'
                }
            }
        }

        stage('Update K8S manifest & push to Repo') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'Github_server', variable: 'GIT_TOKEN')]) {
                        sh '''
                        git config --global user.email bharatht95@outlook.com
                        git config --global user.name Bharaththumma
                        sed -i "s/11/${BUILD_NUMBER}/g" dev/deployment.yaml
                        git add dev/deployment.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git push origin main
                        '''
                    }
                }
            }
        }
    }
}
