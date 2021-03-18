def readProb;
def FAILED_STAGE
pipeline {
agent { label 'master'}
tools {
  git 'Default'
  maven 'mavan-demo'
}
stages {
    stage('Preperation'){
    steps {
        script {
        readProb = readProperties  file:'config.properties'
        FAILED_STAGE=env.STAGE_NAME
        Preperation= "${readProb['Preperation']}"
                if ("$Preperation" == "yes") {
            sh "git config --global user.email zippyops@gmail.com"
        sh "git config --global user.name zippyops"
        sh 'git config --global credential.helper cache'
        sh 'git config --global credential.helper cache'
        sh 'rm -rf devsecopscodebase'
                }
                else {
                 echo "Skipped"
                }
                }
                }
    }
   stage('Git Pull'){
        steps { dir("${readProb['Project_name']}"){
                 git branch: "${readProb['branch']}", credentialsId: "${readProb['credentials']}", url: "${readProb['git.url']}"
              }
                }
         }
        stage("Build") {
           steps {
       script {
            FAILED_STAGE=env.STAGE_NAME
        build= "${readProb['Build']}"
                if ("$build" == "yes") {
               sh """
                cd devsecopscodebase/kubernetes-java
                    mvn clean install
                   """
            }
                 else {
                   echo "Skipped"
                    }
                   }
                 }
        }
     stage('SonarQube analysis') {
          steps {
            script {
         scannerHome = tool 'sonarqube';
             FAILED_STAGE=env.STAGE_NAME
                  SonarQube= "${readProb['SonarQube_Analysis']}"
                if ("$SonarQube" == "yes") {
          withSonarQubeEnv('sonarqube') {
          sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=${readProb['sonar_projectKey']} -Dsonar.projectName=${readProb['sonar_projectName']} -Dsonar.projectVersion=${readProb['sonar_projectVersion']} -Dsonar.projectBaseDir=${readProb['sonar_projectBaseDir']} -Dsonar.sources=${readProb['sonar_sources']} "
           }
            }
                else {
                  echo "Skipped"
                  }
                 }
                }
     }
    stage("Sonarqube Quality Gate") {
           steps {
             script {
            FAILED_STAGE=env.STAGE_NAME
                        Quality= "${readProb['SonarQube_Quality']}"
                    if ("$Quality" == "yes") {
            timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
                        else {
                        echo "skipped"
                        }
                   }
         }
       }
        stage("Jfrog") {
           steps {
       script {
            FAILED_STAGE=env.STAGE_NAME
        jfrog= "${readProb['Jfrog']}"
                if ("$jfrog" == "yes") {
               sh """
                cd devsecopscodebase/kubernetes-java
                    sudo docker rmi javaapp:latest || true
                    sudo docker build -t javaapp:latest .
                    sudo docker tag javaapp:latest jfrog.zippyops.com:8082/docker/kuberentes_java/java-app:$BUILD_NUMBER
            sudo docker push jfrog.zippyops.com:8082/docker/kuberentes_java/java-app:$BUILD_NUMBER
            sudo docker rmi jfrog.zippyops.com:8082/docker/kuberentes_java/java-app:$BUILD_NUMBER
            sed -i s/latest/$BUILD_NUMBER/g $WORKSPACE/devsecopscodebase/kubernetes-java/deploy.yml
            cat $WORKSPACE/devsecopscodebase/kubernetes-java/deploy.yml
            scp -r $WORKSPACE/devsecopscodebase/kubernetes-java root@192.168.1.150:/home/zippyops
                   """
            }
                 else {
                   echo "Skipped"
                    }
                   }
                 }
        }
        stage("Dev Deploy") {
           steps {
           script {
            FAILED_STAGE=env.STAGE_NAME
                  Dev_deploy= "${readProb['Dev_Deploy']}"
                    if ("$Dev_deploy" == "yes") {
                  sh """
                  ssh root@192.168.1.150 "cd /home/zippyops/kubernetes-java/ && kubectl apply -f deploy.yml"
                  echo "http://192.168.8.11:8080/newapp-0.0.1-SNAPSHOT/"
                  """
                  }
                  else {
                  echo "Skipped"
                  }
            }
           }
          }
    }
}
