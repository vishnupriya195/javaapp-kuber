pipeline {
    environment {
        imagename = "vishnupriya96/javaapp-jenkins-training"
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
                git credentialsId: 'github', url: 'git@github.com:vishnupriya195/javaapp-kuber.git'
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
                    dockerImage = docker.build("$imagename","kubernetes-java")
                }
            }
        }
        stage("push Docker image") {
            steps {
                script {
                    docker.withRegistry( '', registryCredentials ) {
                        dockerImage.push("$BUILD_NUMBER")
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage(Removeunusedimages) {
            steps {
                sh "docker rmi $imagename:$BUILD_NUMBER"
                sh "docker rmi $imagename"
            }
        }
        stage("kubedeployment") {
            steps {
                sh "sed -i s/latest/$BUILD_NUMBER/g kubernetes-java/deploy.yml"
                sh "sudo kubectl apply -f kubernetes-java/deploy.yml"
            }
        }
    }

}
