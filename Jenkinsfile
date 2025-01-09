pipeline {
    agent any

    tools {
        maven "Maven"
        jdk "openjdk-11"
    }

    stages {
        stage('Initialize') {
            steps {
                echo "Initializing Pipeline..."
                echo "PATH = ${M2_HOME}/bin:${PATH}"
                echo "M2_HOME = /opt/maven"
            }
        }

        stage('Checkout Code') {
            steps {
                echo "Checking out code..."
                git branch: 'master', url: 'https://github.com/atishayinfo/java-hello-world-webapp.git'
            }
        }

        stage('Build') {
            steps {
                echo "Building the application..."
                sh 'mvn -B -DskipTests clean package'
            }
        }

        stage('Dependency Vulnerability Scan') {
            steps {
                echo "Running OWASP Dependency-Check..."
                sh 'mvn org.owasp:dependency-check-maven:check'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh 'mvn test'
            }
        }

        stage('Code Coverage') {
            steps {
                echo "Generating code coverage report..."
                sh 'mvn jacoco:report'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "Running SonarQube analysis..."
                withSonarQubeEnv('Sonar-Server') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Package') {
            steps {
                echo "Packaging the application..."
                sh 'mvn clean package'
            }
        }
    }

    post {
        always {
            echo "Archiving test results and cleaning up workspace..."
            junit allowEmptyResults: true, testResults: '**/test-results/*.xml'
            cleanWs()
        }
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
