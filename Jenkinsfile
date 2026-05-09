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

                                - name: kubectl
                                  image: bitnami/kubectl:1.35.3
                                  command:
                                    - cat
                                  tty: true

                            volumes:
                                - name: docker-sock
                                  hostPath:
                                    path: /var/run/docker.sock
                    '''
        }
    }

    // triggers {
    //     pollSCM('* * * * *')
    // }

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

        stage('Deploy') {
            steps {
                container('kubectl') {
                    sh "kubectl apply -f ./kubernetes/deployment.yaml"
                    sh "kubectl apply -f ./kubernetes/service.yaml"
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