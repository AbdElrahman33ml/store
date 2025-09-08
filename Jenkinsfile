pipeline {
    agent any

    environment {
        PATH = "./node_modules/.bin:$PATH" // علشان نقدر نشغل sonar-scanner مباشرة
        SONAR_TOKEN = credentials('sonar-token') // التوكن الخاص بـ SonarCloud
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
                sh 'npm install --save-dev @sonar/sonar-scanner'
            }
        }

        stage('Code Analysis') {
            steps {
                timeout(time: 4, unit: 'MINUTES') {
                    sh '''
                    sonar-scanner \
                      -Dsonar.token=${SONAR_TOKEN} \
                      -Dsonar.projectKey=js-ci-project_js-ct \
                      -Dsonar.organization=js-ci-project \
                      -Dsonar.sources=. \
                      -Dsonar.host.url=https://sonarcloud.io
                    '''
                }
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
                    // تنظيف الحاويات القديمة وتشغيل الجديدة
                    sh 'docker rm -f $(docker ps -q) || true'
                    sh 'docker run -d -p 3000:3000 pekker123/crud-123:latest'
                }
            }
        }
    }
}
