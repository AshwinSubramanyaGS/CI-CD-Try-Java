pipeline {
    agent any

    tools {
        maven 'Maven_3.9.11'   // Name from Jenkins "Global Tool Configuration"
        jdk 'Java_21'          // Name from Jenkins "Global Tool Configuration"
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/AshwinSubramanyaGS/CI-CD-Try-Java.git'
            }
        }

        stage('Build JAR') {
            steps {
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Unit Tests') {
            steps {
                bat 'mvn test'
            }
        }

        stage('Version Document') {
            steps {
                bat '''
                echo "Build Version: $(mvn help:evaluate -Dexpression=project.version -q -DforceStdout)" > version.txt
                bat 'echo Build Time: %DATE% >> version.txt'
                '''
                archiveArtifacts artifacts: 'version.txt', followSymlinks: false
            }
        }

        stage('Local Deploy') {
            steps {
        script {
            // List JAR files and grab the first match
            def jarFile = bat(
                script: 'for /f "delims=" %i in (\'dir /B target\\*.jar\') do @echo %i',
                returnStdout: true
            ).trim()

            // Check if file exists
            if (jarFile) {
                echo "Deploying JAR: ${jarFile}"
                bat "java -jar target\\${jarFile} --server.port=9090"
            } else {
                error "No JAR found in target directory!"
            }
        }
    }
        }
    }

    post {
        success {
            echo "Build and Deploy completed successfully!"
        }
        failure {
            echo "Build failed!"
        }
    }
}
