pipeline {
    environment {
        imagename = "sathishbob/javaapp-jenkins-training"
        dockerImage = ''
        registryCredentials = 'dockerhub'
    }
    agent any
    tools {
        maven "MVN3"
        dockerTool "docker"
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
                sh "service docker start"
            }
        }
        stage("kubedeployment") {
            steps {
                sh "kubectl apply -f kubernetes-java/deploy.yml"
            }
        }
    }

}
