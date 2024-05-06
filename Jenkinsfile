pipeline {
    agent any
    environment{
        SONAR_HOME= tool "Sonar"
    }
    stages{
        stage("Clean Workspace"){
            steps{
                cleanWs()
            }
        }
        stage("Clone Code from git"){
            steps{
                git url: "https://github.com/rootmeet/wanderlust.git", branch: "main"
            }
        }
        stage("SonarQube Quality Analysis"){
            steps{
                withSonarQubeEnv("Sonar"){
                    sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=wanderlust -Dsonar.projectKey=wanderlust"
                }
            }
        }
        stage("OWASP dependency check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'dc'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("Sonar Quality Gate Scan"){
            steps{
                timeout(time: 2, unit: "MINUTES"){
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage("Trivy File System scan"){
            steps{
                sh "trivy fs . > trivyfs.txt"
            }
        }
        stage("Deploy with Docker Compose"){
            steps{
                sh "docker-compose down && docker-compose up -d"
            }
        }
        stage("TRIVY Image Scan"){
            steps{
                sh "trivy image sanjeevrisbud/wanderlust-frontend:latest > trivy-frontendimage.txt"
                sh "trivy image sanjeevrisbud/wanderlust-backend:latest > trivy-backendimage.txt" 
            }
        }
        stage("Docker Build & Push"){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker'){  
                        sh "docker push sanjeevrisbud/wanderlust-frontend:latest"
                        sh "docker push sanjeevrisbud/wanderlust-backend:latest"
                    }
                }
            }
        }
    }
}