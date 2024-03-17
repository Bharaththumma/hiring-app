pipeline {
    agent any

        environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        
       
        stage('Docker Build') {
            steps {
                sh "docker build . -t bharaththumma/hiring-app:$BUILD_NUMBER"
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-jenkins', variable: 'hubPwd')]) {
                    sh "docker login -u bharaththumma -p ${hubPwd}"
                    sh "docker push bharaththumma/hiring-app:$BUILD_NUMBER"
                }
            }
        }
        stage('Checkout K8S manifest SCM'){
            steps {
              git branch: 'main', url: 'https://github.com/Bharaththumma/Hiring-app-argocd1.git'
            }
        } 
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([string(credentialsId: 'git_tocken', variable: 'GIT_TOKEN')]) { 
                        sh '''
                        git config --global user.email bharatht95@outlook.com
                        git config --global user.name Bharaththumma
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        sed -i "s/11/${BUILD_NUMBER}/g" /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        git add .
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://$GIT_TOKEN@github.com/Bharaththumma/Hiring-app-argocd1.git main
                        '''                        
                      }
                  }
            }   
        }
            }
} 
