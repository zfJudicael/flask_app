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

                                - name: docker
                                  image: docker
                                  command:
                                    - cat
                                  tty: true
                                  volumeMounts:
                                    - mountPath: /var/run/docker.sock
                                      name: docker-sock
                            volumes:
                                - name: docker-sock
                                  hostPath:
                                    path: /var/run/docker.sock
                    '''
        }
    }

    triggers {
        pollSCM('* * * * *')
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

        stage('Build image') {
            steps {
                container('docker') {
                    sh "docker build -t host.minikube.internal:4000/pythontest:latest ."
                    sh "docker push host.minikube.internal:4000/pythontest:latest"
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