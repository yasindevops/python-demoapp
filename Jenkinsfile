pipeline{
    agent any 
    environment {
        APP_NAME = "py-demoapp"
        RELEASE = "1.0.0"
        DOCKER_USER = "yasindevops06"
        // DOCKER_PASS = 'dockerhub'
        IMAGE_TAG="v${RELEASE}-b${BUILD_NUMBER}"
        IMAGE_NAME="${DOCKER_USER}/${APP_NAME}:${IMAGE_TAG}"
        CONT_NAME="${APP_NAME}"
        JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
        
    }
    stages{
        stage("Test Application"){
            steps {
                script {
                    sh "make test"
                }            
            }
        }
        
        stage('Build App Docker Image') {
            steps {
                echo 'Building App Image'                
                sh 'docker build --force-rm -t "$IMAGE_NAME" .'
                sh 'docker image ls'
            }
       }

        stage('Push Image to Dockerhub Repo') {
            steps {
                echo 'Pushing App Image to DockerHub Repo'
                withCredentials([string(credentialsId: 'my-dockerhub-token', variable: 'DOCKERHUB_TOKEN')]) {
                sh 'docker login -u $DOCKER_USER -p $DOCKERHUB_TOKEN'
                sh 'docker push "$IMAGE_NAME"'                
            }
          }
        }

        stage('Deploy the App') {
            steps {
                echo 'Deploying the App'
                sh 'docker rm -f "$CONT_NAME" || echo "there is no existing container with this name"'
                sh 'docker run --name "$CONT_NAME" -d -p 5000:5000 "$IMAGE_NAME"'          
          
          }
        }

        stage('Destroy the infrastructure'){
            steps{
                timeout(time:5, unit:'DAYS'){
                    input message:'Approve terminate'
                }   
                script {
                   sh 'docker rm -f "$CONT_NAME"' 
                   sh 'docker rmi -f "$IMAGE_NAME"'
                }
            }
        }
    }
}
// post {
//     always {
//         emailext (
//             subject: "Pipeline Status: ${BUILD_NUMBER}",
//             body: '''<html>
//                     <body>
//                     <p>Build Status: ${BUILD_STATUS}</p>
//                     <p>Build Number: ${BUILD_NUMBER}</p>
//                     <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
//                     </body>
//                     </html>''',
//             to: 'yasinhasturk@hotmail.com',
//             from: 'jenkins@noreplay',
//             replyTo: 'yasinhasturk@hotmail.com',
//             mimeType: 'text/html'
//         )
//         }
//     }
// }
