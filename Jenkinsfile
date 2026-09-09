pipeline {

    agent any

    parameters {
        booleanParam(
            name: 'RUN_CODE_STABILITY',
            defaultValue: true,
            description: 'Run Code Stability tests'
        )

        booleanParam(
            name: 'RUN_CODE_QUALITY',
            defaultValue: true,
            description: 'Run Code Quality analysis'
        )

        booleanParam(
            name: 'RUN_CODE_COVERAGE',
            defaultValue: true,
            description: 'Run Code Coverage analysis'
        )
    }

    stages {

        stage('Code Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/rahul96saini/spring3hibernate.git'
            }
        }

        stage('Parallel Scans') {

            parallel {

                stage('Code Stability') {
                    when {
                        expression {
                            params.RUN_CODE_STABILITY
                        }
                    }

                    steps {
                        sh 'mvn test'
                    }
                }

                stage('Code Quality Analysis') {
                    when {
                        expression {
                            params.RUN_CODE_QUALITY
                        }
                    }

                    steps {
                        sh 'mvn checkstyle:check'
                    }
                }

                stage('Code Coverage Analysis') {
                    when {
                        expression {
                            params.RUN_CODE_COVERAGE
                        }
                    }

                    steps {
                        sh 'mvn jacoco:report'
                    }
                }
            }
        }
    }
}
