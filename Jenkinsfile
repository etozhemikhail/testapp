pipeline {
    agent any
    tools {
        jdk "JDK17"
        maven 'maven3'
    }

    parameters {
        string(name: 'repo', defaultValue: '', description: 'link project')
    }

    stages {
        stage ('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage ('checkout git') {
            steps {
                git branch: 'main', url: ${params.repo}
            }
        }

        stage ('build application') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage ('test application') {
            steps {
                sh 'mvn test'
            }
        }

        stage ('sonarqube') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        sh 'mvn sonar:sonar'
                    }
                } 
            }
        }

        stage ('quality gate') {
            steps{
                script {
                    waitForQualityGate(abortPipeline: false, credentialsId: 'sonar-token')
                }
            }
        }
    }
}