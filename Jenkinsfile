pipeline {
    agent any
    
    stages {
        stage('Get code') {
            steps {
                echo 'Traer el codigo'
                git 'https://github.com/PavonMarquez/cp1-helloworld.git'
                echo 'Archivos descargados'
                sh '''
                    ls -la
                    echo $WORKSPACE
                '''
            }
        }
        
        stage('Build') {
            steps {
                echo 'No se compila por ser lenguaje Python'
            }
        }
        
        stage('Tests') {
            parallel {
                stage('Unit') {
            steps {
                sh '''
                    export PYTHONPATH=$WORKSPACE
                    pytest --junitxml=result-unit.xml test/unit
                '''
            }
        }
        
                stage('Rest') {
                    steps {
                        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE'){
                            sh '''
                                export FLASK_APP=app/api.py
                                export FLASK_ENV=development
                                flask run &
                    
                                java -jar /home/jenkins/wiremock/wiremock-jre8-standalone-2.33.2.jar --port 9090 --root-dir /home/jenkins/wiremock &
                                export PYTHONPATH=$WORKSPACE
                    
                                sleep 1
                    
                                pytest --junitxml=result-rest.xml test/rest
                            '''
                        }
                    }
                }
            }
        }
        
        stage('Results') {
            steps {
                junit 'result*.xml'
            }
        }
    }
    post {
            always {
                cleanWs()
            }
        }
}