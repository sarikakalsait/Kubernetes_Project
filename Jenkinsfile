**********************Kubernetes-Project**************************************
node {
    
    stage('git checkout'){
        git 'https://github.com/sarikakalsait/Kubernetes_Project'
    }
    
    stage('sending docker file to ansible server over ssh'){
     sshagent(['ansible_demo']) {
         sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.44.127'
         sh 'scp /var/lib/jenkins/workspace/pipeline-demo/* ubuntu@172.31.44.127:/home/ubuntu/'

        }   
    }
    stage('Docker Build Images'){
        sshagent(['ansible_demo']) {
         sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.44.127 cd /home/ubuntu/'
         sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.44.127 docker image build -t $JOB_NAME:v1.$BUILD_ID .'
        }
    }
    stage('Docker Images tagging'){
        sshagent(['ansible_demo']) {
         sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.44.127 cd /home/ubuntu/'
         sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.44.127 docker image tag $JOB_NAME:v1.$BUILD_ID 80858086/$JOB_NAME:v1.$BUILD_ID '
         sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.44.127 docker image tag $JOB_NAME:v1.$BUILD_ID 80858086/$JOB_NAME:v1.lastest '
        }
    }
    stage('Push Docker Images to DockerHUB'){
      sshagent(['ansible_demo']) {
            withCredentials([string(credentialsId: 'dockerhub_passwd', variable: 'dockerhub_passwd')]) {
                sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.44.127 docker login -u 80858086 -p ${dockerhub_passwd}"
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.44.127 docker image push 80858086/$JOB_NAME:v1.$BUILD_ID '
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.44.127 docker image push 80858086/$JOB_NAME:v1.lastest '
            }
        }
        
    }
    stage('COPY FILES FROM ANSIBLE TO K8S SERVER'){
      sshagent(['kubernetes_server']) {
              sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.57.216'
              sh 'scp /var/lib/jenkins/workspace/pipeline-demo/* ubuntu@172.31.57.216:/home/ubuntu/'
      }
        
    }
     stage('Kubernetes Deployemt using ansible-playbook'){
      sshagent(['ansible_demo']) {
         sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.44.127 cd /home/ubuntu/'
         sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.44.127 ansible -m ping node'
         sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.44.127 ansible-playbook ansible.yml'
        }
    }
}


*******************************************************************
