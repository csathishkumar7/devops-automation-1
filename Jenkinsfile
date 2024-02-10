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
                script{
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'k8sconfigpwd')
                }
            }
        } 
    }
}