pipeline {
    agent any
    
    stages{
        stage('SonarQube Analysis') {
      steps {
        script {
          // requires SonarQube Scanner 2.8+
          scannerHome = tool 'SonarScanner'
        }
        withSonarQubeEnv('SonarQube Server') {
          sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=CICD-101"
        }
      }
    }

        stage('Build Docker Image') {
            steps {
                script{
                    sh 'docker build -t ajk598/python-http-server .'
            }
        }
    }
        stage('Containerize And Test') {
            steps {
                script{
                    sh 'docker run -d --name python-app ajk598/python-http-server && sleep 10 && docker stop python-app'
                }
            }
        }
        stage('Push Image To Dockerhub') {
            steps {
                script{
                    withCredentials([string(credentialsId: 'DockerHubPass', variable: 'DockerHubpass')]) {
                    sh 'docker login -u ajk598 --password ${DockerHubpass}' }
                    sh 'docker push ajk598/python-http-server'
                }
            }
        }    
}
        post {
        always {
            // Always executed
                sh 'docker rm python-app'
        }
        success {
            // on sucessful execution
            sh 'docker logout'   
        }
    }
}