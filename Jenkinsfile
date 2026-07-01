pipeline {
    agent any

    tools {
        maven 'maven1'
    }

    stages {
        stage('checkout') {
            steps {
                git 'https://github.com/saathidavane/calwebapp.git'
                echo 'clonning is succesfull'
            }
        }

        stage('build'){
            steps {
                sh '''
                ls -la
                mvn clean package
                ls -la
                '''
            }

            post {
                success {
                    archiveArtifacts 'target/*.war'
                    sh 'ls -la'

                }
            }
        }

        stage('sonarqube-testing'){
            steps {
                withSonarQubeEnv('sonar') {
                    sh 'mvn sonar:sonar'
                }

            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        try {
                            def qg = waitForQualityGate()
                            echo "Quality Gate Status: ${qg.status}"
                            if (qg.status != 'OK') {
                                error "Quality Gate failed: ${qg.status}"
                            }
                        } catch (Exception e) {
                            echo "Quality Gate check failed: ${e.message}"
                            error "Quality Gate stage failed"
                        }
                    }
                }

            }
        }

        stage('SonarQube Report'){
            steps {
                script {
                    echo "SonarQube Report successfully generated"
                }
            }
        }

        stage('Docker Build and Push to ECR') {
            steps {
                sh '''
                ls -la
                docker build -t calwebapp:v1 .
                ls -la
                docker images
                aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 139023233927.dkr.ecr.ap-south-1.amazonaws.com
                docker tag calwebapp:v1 139023233927.dkr.ecr.ap-south-1.amazonaws.com/web-apps:latest
                docker push 139023233927.dkr.ecr.ap-south-1.amazonaws.com/web-apps:latest 
                '''
            }
        }

    }
}
