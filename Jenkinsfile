pipeline {
    agent any
    tools{
        maven 'maven_3_5_0'
    }
    stages{
        stage('Build Maven'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/csathishkumar7/devops-automation-1.git']])
                sh 'mvn clean install'
            }
        }
    stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t sathishcsk/devops-integration .'
                }
            }
        } 
    stage('Push image to Hub'){
            steps{
                script{
                   withCredentials([string(credentialsId: 'dockerpwd', variable: 'dockerhubpwd')]) {
                   sh 'docker login -u sathishcsk -p ${dockerhubpwd}'

}
                   sh 'docker push sathishcsk/devops-integration'
                }
            }
        }
    stage('Deploy to k8s'){
            steps{
                sshagent(['k8sconfigpwd']){
                    sh "scp -o StrictHostKeyChecking=no deploymentservice.yaml ubuntu@172.27.10.123:/home/ubuntu"    
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'k8sconfigpwd')
                    }
                script{
                    try{
                    sh "ssh ubuntu@172.27.10.123 kubectl apply -f ."
                }catch(error){
                    sh "ssh ubuntu@172.27.10.123 kubectl create -f ."
            }     
                }
            }
        } 
    }
}
