pipeline {
    agent any
    
    stages {
        
        stage('Code Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/rahul96saini/spring3hibernate.git'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Code Analysis') {
            parallel {
                
                stage('Code Stability') {
                    steps {
                        echo 'Running code stability analysis'
                    }
                }
                
                stage('Code Quality Analysis') {
                    steps {
                        echo 'Running code quality analysis'
                    }
                }
                
                stage('Code Coverage Analysis') {
                    steps {
                        echo 'Running code coverage analysis'
                    }
                }
            }
        }
    }
}
