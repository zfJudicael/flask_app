pipeline {
    agent {
        kubernetes {
            label 'jenkins-agent-my-app'
            yaml '''
                        apiVersion: v1
                        kind: Pod
                        metadata:
                            labels:
                                component: ci
                        spec:
                            containers:
                                - name: python
                                  image: python:3
                                  command:
                                    - cat
                                  tty: true
                    '''
        }
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Test python') {
            steps {
                container('python') {
                    sh "pip install -r requirements.txt"
                    sh "python test.py"
                }
            }
        }

    }

    post {
        always {
            echo 'Pipeline terminé...'
        }

        success {
            echo 'Tests passés...'
        }

        failure {
            echo 'Tests échoués...'
        }

    }
}