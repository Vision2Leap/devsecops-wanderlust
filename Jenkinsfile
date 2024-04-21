pipeline {
    agent any
    
    environment{
        SCANNER_HOME = tool "Sonar"
    }

    parameters{
        choice(name: "action", choices: "create\ndestroy", description: "Create or Destroy pipeline")
    }

    stages {
        stage('Clean Workspace') {
            when { expression { params.action == "create" } }
                steps {
                    script{    
                        cleanWs()
                    }
                }
        }
        
        stage('Code Checkout') {
            when { expression { params.action == "create" } }
                steps {
                    git url: "https://github.com/LondheShubham153/wanderlust.git", branch: "feat-131-dockerize"
                }
        }

        stage('Test cases') {
            when { expression { params.action == "create" } }
                parallel{
                    stage("Frontend Test") {
                        steps{
                            dir("frontend/"){
                                sh "npm run test"
                            }
                        }        
                    }

                    stage("Backend Test") {
                        steps{
                            dir("backend/"){
                                sh "npm run test"
                            }
                        }        
                    }
                }
        }
        
        stage('Code Quality Check: SonarQube') {
            when { expression { params.action == "create" } }
                steps {
                    withSonarQubeEnv("Sonar"){
                        sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Wanderlust -Dsonar.projectName=Wanderlust"
                    }
                }
        }

        stage('Quality Gates Check: SonarQube') {
            when { expression { params.action == "create" } }
                steps {
                    timeout(time: 1, unit: "MINUTES"){
                       waitForQualityGate abortPipeline: false
                   }
             }
        }

        stage('OWASP Dependency Check') {
            when { expression { params.action == "create" } }
                steps {
                        dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'dc'
                        dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                   }
        }

        stage('Code build: Docker') {
            when { expression { params.action == "create" } }
                parallel{
                    stage("Frontend"){
                        steps{
                            dir("frontend/"){
                                sh "docker build -t madhupdevops/frontend-wanderlust:latest"
                            }
                        }
                    }

                    stage("Backend"){
                        steps{
                            dir("backend/"){
                                sh "docker build -t madhupdevops/backend-wanderlust:latest"
                            }
                        }
                    }
                }
            }       
        }
    }
