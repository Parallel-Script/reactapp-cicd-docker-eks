pipeline {
  agent any
  
   tools {nodejs "node"}
    
  stages {
    stage("Clone code from GitHub") {
            steps {
                script {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'GITHUB_CREDENTIALS', url: 'https://github.com/Parallel-Script/reactapp-cicd-docker-eks']])
                }
            }
        }
     
    stage('Node JS Build') {
      steps {
        sh 'npm install'
      }
    }
  
     stage('Build Node JS Docker Image') {
            steps {
                script {
                  sh 'docker build -t veeruved/node-app-1.0 .'
                }
            }
        }


        stage('Deploy Docker Image to DockerHub') {
            steps {
                script {
                 withCredentials([string(credentialsId: 'veeruveddocker', variable: 'veeruveddocker')]) {
                    sh 'docker login -u veeruved -p ${veeruveddocker}'
            }
            sh 'docker push veeruved/node-app-1.0'
        }
            }   
        }
         
     stage('Deploying Node App to Kubernetes') {
      steps {
        script {
          sh ('aws eks update-kubeconfig --name demo-cluster-1 --region us-east-1')
          sh "kubectl get ns"
          sh "kubectl apply -f nodejsapp.yaml"
        }
      }
    }

    post {
        success {
            emailext(
                subject: "Deployment Successful",
                body: "The deployment to EKS was successful!",
                to: "patilvaradraj18@gmail.com"
            )
        }
        failure {
            emailext(
                subject: "Deployment Failed",
                body: "The deployment to EKS failed. Please check the logs.",
                to: "patilvaradraj18@gmail.com"
            )
        }
    }

  }
}
