pipeline {
    agent any

    environment {

        JAVA_HOME = 'C:\\Program Files\\Java\\jdk-21.0.12.1'

        MAVEN = 'C:\\maven\\apache-maven-3.10.0-bin\\apache-maven-3.10.0\\bin\\mvn.cmd'

        PATH = "${JAVA_HOME}\\bin;${env.PATH}"
        
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/harshitshrivastava2020/test_project04.git', branch: 'main'
            }
        }

        stage('Build New WAR') {
            steps {
                bat '"%MAVEN%" clean package'
            }
        }
        
        stage('Docker Build and Registry Push') {
            steps {
                bat 'docker-compose pull db'
                bat 'docker-compose build webapp'
                
                // Fixed: No spaces around the password injection to prevent trailing character issues
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DH_USER',
                        passwordVariable: 'DH_TOKEN'
                    )
                ])
{
                    // Uses triple single quotes for complex multiline Windows batch blocks cleanly
                    bat '''
                        @echo off
                        echo %DH_TOKEN%| docker login --username "%DH_USER%" --password-stdin
                        
                        docker tag mysql:8.0 %DH_USER%/mysql:8.0
                        docker tag webapp:latest %DH_USER%/webapp:latest
                        
                        docker push %DH_USER%/mysql:8.0
                        docker push %DH_USER%/webapp:latest
                    '''
                }
            }
        }

        stage('Deploy Using Docker Compose') {
            steps {
                    bat 'docker-compose down || docker-compose.exe down'
            }
        }
    }
}
