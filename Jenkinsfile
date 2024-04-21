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
        
        stage('Code Quality Check: SonarQube') {
            when { expression { params.action == "create" } }
                steps {
                    withSonarQubeEnv("Sonar"){
                        sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Wanderlust -Dsonar.projectName=Wanderlust"
                    }
                }
        }
    }
}
