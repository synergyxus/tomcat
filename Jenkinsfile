def git_commit = ''
def image = ''
pipeline {
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr:'10'))
    }


    stages {
        stage("Docker Build Image") {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-creds') {
                        image = docker.build("synergyx/demo-tomcat-app")
                    }
                }
            }
        }

        stage("Publish Image to Docker Hub") {
            steps {
                script {
                    git_commit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                    tag = "${branch_name}-${git_commit}"
                    image.push(tag)
                }
            }
        }
        stage("Run newly created Docker image in Kubernetes Cluster") {
            steps {
                script {
                    image_name = "synergyx/demo-tomcat-app:${tag}"
                    sh "kubectl set image deployment/demo-tomcat-app demo-nodejs-app=${image_name}"
                    sh "kubectl rollout status deployment/demo-tomcat-app"
                }
            }
        }
    
    }
}
