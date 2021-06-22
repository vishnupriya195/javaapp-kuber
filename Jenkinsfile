pipeline {
    environment {
        imagename = "sathishbob/jenkins-javaapp-training:test"
        dockerImage = ''
        registryCredentials = 'dockerhub'
    }
    agent any
    tools {
        maven 'MVN3'
        dockerTool 'docker'
    }
    
    stages {
        stage("pullscm") {
            steps {
                git credentialsId: 'github', url: 'git@github.com:sathishbob/javaapp-kuber.git'
            }
        }
        stage("build") {
            steps {
                sh "mvn -f kubernetes-java clean install"
            }
        }
        stage("Build Docker Image") {
            steps {
                script {
                    dockerImage = docker.build("$imagename", "kubernetes-java")
                }
            }
        }
        stage("Push Docker Image") {
            steps {
                script {
                    docker.withRegistry( '', registryCredentials ) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage("Cleanup") {
            steps {
                sh "docker rmi $imagename"
            }
        }
        stage("pullrepoonlinuxnode") {
            agent { label 'linux' }
            steps {
                git credentialsId: 'github', url: 'git@github.com:sathishbob/javaapp-kuber.git'
            }
        }
        stage("kubeDeployment") {
            agent { label 'linux' }
            steps {
                sh "kubectl apply -f kubernetes-java/deploy.yml"
            }
        }
    }
}
