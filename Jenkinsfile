pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'python3 -m py_compile sources/prog.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {
            steps {
                sh 'pytest -v --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit "test-reports/results.xml"
                }
            }
        }
        stage('Deliver') {
            steps {
                dir(path: env.BUILD_ID) {
                    unstash(name: 'compiled-results')
                    sh 'pyinstaller -F sources/prog.py'
                }
            }
            post {
                success {
                    archiveArtifacts "${env.BUILD_ID}/dist/prog"
                }
            }
        }
        stage('Branch') {
            agent any
            steps {
                echo "${env.WORKSPACE}"
                echo "${env.BUILD_ID}"
            }
        }

    }
}