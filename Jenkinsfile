pipeline {
    agent any

    tools {
        // Install the Maven version configured as "maven-398" and add it to the path.
        maven "maven-398"
    }

    stages {
        stage('Check Maven') {
            steps {
                sh "if ! command -v mvn; then echo 'Maven not found!'; exit 1; fi"
            }
        }

        stage('Build') {
            steps {
                git branch: 'main', url: 'https://github.com/Avdunusinghe/Java-Maven-Jenkins.git'
                sh "mvn clean package -DskipTests=true"
            }
        }

        stage('Unit Test') {
            steps {
                sh "mvn test"
            }
        }
    }
}
