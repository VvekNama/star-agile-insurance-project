pipeline {
    agent any
    
environment {
       DOCKERHUB_CREDENTIALS = credentials('Docker-Token') 
        DOCKER_IMAGE_NAME = 'insurance:latest'
        DOCKERHUB_REPO = 'vvek24/insurance'
       // K8S_TOKEN = credentials('k8s-token')
       //  KUBECONFIG = credentials('k8s-token')
       // KUBECONFIG = credentials('k8s-token') 
        CONTAINER_NAME = "insurance-container"  
    
        }
    
    stages {
        stage('Clone Repository') {
        
                 steps {
                git(
                    url: 'https://github.com/VvekNama/star-agile-insurance-project', branch: 'master',
                    credentialsId: 'Github-Token'
                )
            
                
            }
        }
        
        stage('Remove Existing Container') {
            steps {
                script {
                    // Check if the container exists, then remove it
                    def containerExists = sh(script: "docker ps -a -q --filter name=${CONTAINER_NAME}", returnStdout: true).trim()
                    if (containerExists) {
                        echo "Container ${CONTAINER_NAME} exists, removing it."
                        sh "docker stop ${CONTAINER_NAME}"
                        sh "docker rm ${CONTAINER_NAME}"
                    } else {
                        echo "No existing container to remove."
                    }
                }
            }
        }

        //cvb
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Dockerize') {
            steps {
                sh 'docker build -t insurance:latest .'
            }
        }
        
        stage('Push Docker Image') {
            steps {
                sh """
                    echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin
                    docker tag ${DOCKER_IMAGE_NAME} ${DOCKERHUB_REPO}
                    docker push ${DOCKERHUB_REPO}
                """
                
                sh 'docker tag medicure:latest docker.io/vvek24/insurance:latest'
                sh 'docker push docker.io/vvek24/insurance:latest'
            }
        }


         stage('Run Docker Container') {
            steps {
                script {
                    // Run the container in detached mode
                    sh """
                        docker run -d --name ${CONTAINER_NAME} -p 9094:8081 ${DOCKER_IMAGE_NAME}
                    """
                }
            }
        }
        
        // stage('Deploy to Kubernetes') {
        //     steps {

        //      // script {
        //      //        // Decode kubeconfig and apply deployment
        //      //        sh '''#!/bin/bash
        //      //        echo "$KUBECONFIG" | base64 -d > /tmp/kubeconfig
        //      //        kubectl --kubeconfig=/tmp/kubeconfig apply -f k8s/deployment.yml --validate=false
        //      //        '''
        //      //    }
                
        //         sh 'kubectl apply -f k8s/deployment.yml --validate=false'
        //         sh 'kubectl apply -f k8s/service.yml --validate=false'
        //     }
        // }
     }
}
