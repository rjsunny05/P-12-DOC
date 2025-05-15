pipeline {
    agent any

    environment {
        SONAR_SCANNER_HOME = tool 'SonarQubeScanner'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/OT-MICROSERVICES/attendance-api.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    curl -sSL https://install.python-poetry.org | python3 -
                    export PATH="$HOME/.local/bin:$PATH"
                    poetry install
                '''
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh '''
                    export PATH="$HOME/.local/bin:$PATH"
                    poetry run pytest tests/ --cov=src --cov-report=xml
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh """
                        ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=attendance-api \
                        -Dsonar.sources=src \
                        -Dsonar.host.url=http://107.20.119.99:9000 \
                        -Dsonar.login=sqa_ed8c672dc4770f06e66c07b5699072ceae029258 \
                        -Dsonar.python.coverage.reportPaths=coverage.xml
                    """
                }
            }
        }

        stage('Quality Gate Check') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
