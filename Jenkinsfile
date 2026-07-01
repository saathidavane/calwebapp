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

    }
}
