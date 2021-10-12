pipeline {
    agent any
    environment {
        PROJECT_ID = 'seismic-octane-322420'
        CLUSTER_NAME = 'CLUSTER-NAME'
        LOCATION = 'us-east1'
        CREDENTIALS_ID = 'gke'
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub') {
                        def customImage = docker.build("skarra9821/node-app:${env.BUILD_ID}")
                        customImage.push()
                    }
                }
            }
        /*stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }*/        
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }   
}

