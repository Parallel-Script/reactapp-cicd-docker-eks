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
          sh ('aws eks update-kubeconfig --name rappcluster --region us-east-1')
          sh "kubectl get ns"
          sh "kubectl apply -f nodejsapp.yaml"
        }
      }
    }

   
  }

post { 
    always { 
        script { 
            def jobName = env.JOB_NAME 
            def buildNumber = env.BUILD_NUMBER 
            def pipelineStatus = currentBuild.result ?: 'UNKNOWN' 
            def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red' 
             
            def body = """<html> 
                            <body> 
                                <div style="border: 4px solid ${bannerColor}; padding: 10px;"> 
                                    <h2>${jobName} - Build ${buildNumber}</h2> 
                                    <div style="background-color: ${bannerColor}; padding: 10px;"> 
                                        <h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3> 
                                    </div> 
                                    <p>Check the <a href="${BUILD_URL}">console output</a>.</p> 
                                </div> 
                            </body> 
                        </html>""" 
            emailext ( 
                subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}", 
                body: body, 
                to: 'veeruved186@gmail.com', 
                from: 'jenkins@example.com', 
                replyTo: 'jenkins@example.com', 
                mimeType: 'text/html', 
                 
            ) 
        } 
    } 
}

}
