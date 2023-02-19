pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'python3 -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') {
            steps {
                sh 'python3 -m pytest --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input(
                        id: 'userInput',
                        message: 'Do you want to proceed to the deployment stage?',
                        parameters: [
                            [$class: 'BooleanParameterDefinition', defaultValue: false, name: 'proceed'],
                            [$class: 'BooleanParameterDefinition', defaultValue: false, name: 'abort']
                        ]
                    )
                    if (userInput.proceed) {
                        echo 'Continue to deploy stage'
                    } else if (userInput.abort) {
                        echo 'aborting the process'
                        error('Execution aborted by user')
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                sh 'pyinstaller --onefile sources/add2vals.py'
                sleep time: 60, unit: 'SECONDS'
                sh './jenkins/scripts/kill.sh'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}