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
        
        stage('Checkout K8S manifest SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/betawins/Hiring-app-argocd.git'
            }
        } 
        
        stage('Update K8S manifest & push to Repo') {
    steps {
        script {
            withCredentials([usernamePassword(credentialsId: 'Github_server', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                sh """
                ls -la $WORKSPACE/dev  # Print contents of dev directory
                cat $WORKSPACE/dev/deployment.yaml  # Print contents of deployment.yaml
                sed -i "s/6/${BUILD_NUMBER}/g" $WORKSPACE/dev/deployment.yaml
                cat $WORKSPACE/dev/deployment.yaml
                git add .
                git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                git remote -v
                git push https://$GIT_USERNAME:$GIT_PASSWORD@github.com/betawins/Hiring-app-argocd.git main
                """
            }
        }
    }
}
