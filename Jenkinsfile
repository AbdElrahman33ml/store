


pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('sonar-token') // اسم الـ Credential اللي فيه التوكن
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Code Analysis') {
            steps {
                sh '''
                npm install -g @sonar/scan
                sonar \
                  -Dsonar.token=${SONAR_TOKEN} \
                  -Dsonar.projectKey=js-ci-project_js-ct \
                  -Dsonar.organization=js-ci-project \
                  -Dsonar.sources=. \
                  -Dsonar.host.url=https://sonarcloud.io
                '''
            }
        }

 

       stage('Docker Build And Push') {
           steps {
               script {
                   def imageName = "pekker123/crud-123"
                   def imageTag = "latest"

                    docker.withRegistry('https://index.docker.io/v1/', 'docker-cred') {
                        def image = docker.build("${imageName}:${imageTag}")
                        image.push()

                    }
                }
            }
        }
    
       
        stage('Deploy To EC2') {
            steps {
                script {
                        sh 'docker rm -f $(docker ps -q) || true'
                        sh 'docker run -d -p 3000:3000 pekker123/crud-123:latest'
                        
                    
                }
            }
        }
        
}
}
