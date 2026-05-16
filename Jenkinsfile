pipeline {
    agent any
    tools {
        maven "maven-398"
        jdk "jdk17"
    }

    stages {
        stage('Check Maven') {
            steps {
                sh "if ! command -v mvn; then echo 'Maven not found!'; exit 1; fi"
            }
        }

        stage('Check Java') {
            steps {
                sh 'java -version'
                sh 'javac -version'
                sh 'echo $JAVA_HOME'
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
                junit stdioRetention: '', testResults: 'target/surefire-reports/TEST-*.xml'
            }
        }

        stage('Local Deployment') {
            steps {
                // Kill any previous instance to avoid port conflicts
                sh "pkill -f 'hello-demo' || true"
                sh "sleep 2"
                sh "nohup java -jar target/hello-demo-*.jar > /tmp/app.log 2>&1 &"
            }
        }

        stage('Integration Testing') {
            steps {
                // Wait until app is ready (up to 60 seconds)
                sh """
                    echo 'Waiting for app to start...'
                    for i in \$(seq 1 60); do
                        if curl -s http://localhost:${params.APPLICATION_PORT}/hello > /dev/null; then
                            echo 'App is up!'
                            break
                        fi
                        echo "Attempt \$i - not ready yet..."
                        sleep 1
                    done
                """
                sh "curl -s http://localhost:${params.APPLICATION_PORT}/hello | grep -i 'Hello, Ashen!'"
            }
        }
    }

    post {
        always {
            sh "pkill -f 'hello-demo' || true"
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
            sh "cat /tmp/app.log || true"
        }
    }
}
