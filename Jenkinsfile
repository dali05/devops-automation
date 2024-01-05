pipeline {
    agent any
    tools{
        maven 'maven'
    }
    
    stages{
        stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/dali05/devops-automation.git']]])
                sh 'mvn clean install'
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t dali05/kubernetes .'
                }
            }
        }
        stage('Push image to hub'){
            steps{
                script{
                    withCredentials([usernameColonPassword(credentialsId: '76d44953-76e8-4cfd-bd38-85a9cb9eded5', variable: 'DockerCredentials')]) {
                        def (dockerUsername, dockerPassword) = DockerCredentials.split(':')
                        sh "docker login -u $dockerUsername -p $dockerPassword"
                    }
                    sh 'docker push dali05/kubernetes'
                }
            }
        }
        stage('Deploy to K8s'){
            steps{
                script{
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'kubeconfig')
                }
            }
        }
    
    }    
}
