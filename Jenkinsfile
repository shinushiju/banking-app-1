pipeline {

    agent any

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shinushiju/banking-app-1.git'
            }
        }

        stage('Build Docker') {
            steps {
                sh 'docker build -t banking-app-1 .'
            }
        }

        stage('Docker Tag') {
            steps {
                sh 'docker tag banking-app-1 shinushiju/banking-app-1:${BUILD_NUMBER}'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {

                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push shinushiju/banking-app-1:${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Deploy EKS') {
            steps {
                sh '''
                sed -i "s|IMAGE_PLACEHOLDER|shinushiju/banking-app-1:${BUILD_NUMBER}|g" deployment.yaml
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }
}
