pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git branch: 'main', 
                    url: 'https://github.com/Sravani-Meduri/Django-todoapp.git'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t sravanif5/todoapp:v${IMAGE_TAG} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push sravanif5/todoapp:v${IMAGE_TAG}
                    '''
                }
            }
        }

         stage('Updates the k8 manifests and push to gitrepo'){
           steps{
                script{
                    withCredentials
                    ([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) 
                    {
                        sh '''
                        cat k8s/deployment.yaml
                        sed -i "s|image: sravanif5/todoapp:.*|image: sravanif5/todoapp:v${IMAGE_TAG}|g" k8s/deployment.yaml
                        cat k8s/deployment.yaml
                        git config user.email "jenkins@local"
                        git config user.name "Jenkins"
                        git add k8s/deployment.yaml
                        git commit -m "Updated deployment.yaml to v${IMAGE_TAG}" || true
                        git remote -v
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/Sravani-Meduri/Django-todoapp.git HEAD:main
                    
                        '''
                    }
                }
            }
        }
        
        
    }
}
