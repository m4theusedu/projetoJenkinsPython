pipeline {
    agent any

    environment {
        PYTHON = "C:\\Users\\Diego\\AppData\\Local\\Python\\pythoncore-3.14-64\\python.exe"
        JENKINS_NODE_COOKIE = "dontKillMe"
    }

    stages {
        stage('Preparar Ambiente') {
            steps {
                // Cria o ambiente virtual de forma segura apenas se ele não existir
                bat 'if not exist venv python -m venv venv'
                bat '.\\venv\\Scripts\\activate && python -m pip install --upgrade pip && pip install -r requirements.txt'
            }
        }

        stage('Executar Testes') {
            steps {
                // Ativa a venv existente e roda os testes reais gerando os relatórios esperados pelo pós-build
                bat '.\\venv\\Scripts\\activate && pytest --junitxml=relatorio-testes.xml --cov=app --cov-report=xml --cov-report=html'
            }
        }

        // stage('Analise de Vulnerabilidades') {
        //     steps {
        //         dependencyCheck(
        //             odcInstallation: 'DependencyCheck',
        //             additionalArguments: '--scan .'
        //         )
        //     }
        // }

        stage('Build') {
            steps {
                bat """
                    call venv\\Scripts\\activate.bat
                    python -m compileall app
                """
            }
        }

        stage('Parar Aplicacao Antiga') {
            steps {
                bat """
                    C:\\Windows\\System32\\taskkill.exe /F /FI "WINDOWTITLE eq calculadora-jenkins" /T >nul 2>&1
                    exit /B 0
                """
            }
        }

        stage('Iniciar Aplicacao') {
            steps {
                bat """
                    set JENKINS_NODE_COOKIE=dontKillMe
                    start "calculadora-jenkins" cmd /c "call venv\\Scripts\\activate.bat && python -m app.main"
                    C:\\Windows\\System32\\ping.exe 127.0.0.1 -n 6 >nul
                """
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, testResults: 'relatorio-testes.xml'

            publishHTML([
                allowMissing: true,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'htmlcov',
                reportFiles: 'index.html',
                reportName: 'Cobertura de Testes'
            ])
        }
    }
}