pipeline {
    agent any

    environment {
        PYTHON = "C:\\Users\\Diego\\AppData\\Local\\Python\\pythoncore-3.14-64\\python.exe"
    }

    stages {
        stage('Preparar ambiente') {
            steps {
                bat """
                    "%PYTHON%" -m venv venv
                    call venv\\Scripts\\activate.bat
                    python -m pip install --upgrade pip
                    python -m pip install -r requirements.txt
                """
            }
        }

        stage('Executar testes') {
            steps {
                bat """
                    call venv\\Scripts\\activate.bat
                    python -m pytest --junitxml=relatorio-testes.xml
                """
            }
        }

        stage('Build') {
            steps {
                bat """
                    call venv\\Scripts\\activate.bat
                    python -m compileall app
                """
            }
        }

        stage('Iniciar aplicacao') {
            steps {
                bat """
                    taskkill /F /IM python.exe /T || exit /B 0
                    call venv\\Scripts\\activate.bat
                    start /B python -m app.main
                """
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, testResults: 'relatorio-testes.xml'
        }
    }
}