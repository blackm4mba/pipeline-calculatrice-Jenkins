pipeline {
    agent none
    stages {
        stage('Build') {
            agent any
            steps {
                sh 'python3 -m py_compile sources/prog.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {
            agent any
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
            agent any
            steps {
                dir(path: env.BUILD_ID) {
                    unstash(name: 'compiled-results')
                    sh 'mkdir sources/dist'
                    sh 'pyinstaller -F sources/prog.py'
                }
            }
            post {
                success {
                    archiveArtifacts "${env.BUILD_ID}/sources/dist/prog"
                }
            }
        }

    }
}