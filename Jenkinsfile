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

    }
}
