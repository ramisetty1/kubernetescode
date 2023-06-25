pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('DOCKER_HUB')
    }

    stages {
        stage('checkout') {
            steps {
                echo 'cloning stage'
                git branch: 'main', url: 'https://github.com/ramisetty1/kubernetescode.git'
            }
        }
        stage('docker build') {
            steps {
                script {
                    echo 'build the docker image'
                    sh 'docker build -t flaskapp .'
                    sh 'docker tag flaskapp chukkap/flaskapp' + ":$BUILD_NUMBER"
                }
            }
        }
        stage('docker login') {
            steps {
                echo 'docker login'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('push docker image') {
            steps {
                echo 'push image'
                sh 'docker push chukkap/flaskapp' + ":$BUILD_NUMBER"
            }
        }
        stage('Trigger ManifestUpdate') {
            steps {
                echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
            }
            
        }
    }
}
