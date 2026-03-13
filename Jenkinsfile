pipeline {
    agent any
    environment{
        SONAR_HOME= tool "sonar"
    }

    stages {
        stage('Code Clone') {
            steps {
               git branch: 'main', url: 'https://github.com/juhisinha422/Wanderlust-CI-CD-Project.git'
            }
        }
        stage('SonarQube Quality Analysis') {
            steps {
                withSonarQubeEnv("sonar"){
                    sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=wanderlust -Dsonar.projectKey=wanderlust"
                }
            }
        }
        
        
        stage('Docker Image build') {
            steps {
                sh "docker build --no-cache -t frontend ./frontend"
                sh "docker build --no-cache -t backend ./backend"

            }
        }
        stage('Trivy Image Scan') {
          steps {
             sh "trivy image frontend:latest"
             sh "trivy image backend:latest"
    }
}
        
        
        stage('Backend & Frontend Image Push to Docker Hub  ') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'dockerHUbCred', passwordVariable: 'dockerpass', usernameVariable: 'dockeruser')]) {
            sh "docker login -u $dockeruser -p $dockerpass"
            sh "docker image tag frontend:latest $dockeruser/frontend:v$BUILD_NUMBER"
            sh "docker image tag frontend:latest $dockeruser/frontend:latest"
            sh "docker image tag backend:latest $dockeruser/backend:v$BUILD_NUMBER"
            sh "docker image tag backend:latest $dockeruser/backend:latest"
            
            sh "docker push $dockeruser/frontend:v$BUILD_NUMBER"
            sh "docker push $dockeruser/frontend:latest"
            sh "docker push $dockeruser/backend:v$BUILD_NUMBER"
            sh "docker push $dockeruser/backend:latest"
        }
    }
}
          stage('Database Deploy') {
            steps {
                    sh "docker rm -f mongodb || true"
                    sh "docker run -d -p 27017:27017 -v mongodb_data:/data/db --name mongodb --network wandernet mongo:latest"

            }
        }
        
         stage('Deploy using docker images ') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHUbCred', passwordVariable: 'dockerpass', usernameVariable: 'dockeruser')]) {
                     sh "docker rm -f backend-con || true"
                     sh "docker rm -f frontend-con || true"
                     sh "docker run -d -p 5000:5000 --name backend-con --network wandernet $dockeruser/backend:latest"
                     sh "docker run -d -p 5173:5173 --name frontend-con --network wandernet $dockeruser/frontend:latest"


            }
        }
        
        



        
        
          
         
        
   }     
    
}
}

