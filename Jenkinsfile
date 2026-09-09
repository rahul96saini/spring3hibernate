def publicationApproved = false

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

                    post {
                        always {
                            junit 'target/surefire-reports/*.xml'
                        }
                    }
                }

                stage('Code Quality Analysis') {
                    when {
                        expression {
                            params.RUN_CODE_QUALITY
                        }
                    }

                    steps {
                        sh 'mvn checkstyle:checkstyle'
                    }

                    post {
                        always {
                            archiveArtifacts artifacts: 'target/checkstyle-result.xml',
                                               fingerprint: true
                        }
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

                    post {
                        always {
                            jacoco execPattern: 'target/jacoco.exec'
                        }
                    }
                }
            }
        }

        stage('Approval') {
            steps {
                script {
                    def decision = input(
                        message: 'Approve artifact publication?',
                        ok: 'Submit',
                        parameters: [
                            choice(
                                name: 'APPROVAL',
                                choices: ['Approve', 'Deny'],
                                description: 'Choose whether to publish the artifact'
                            )
                        ]
                    )

                    echo "Publication decision: ${decision}"

                    if (decision == 'Approve') {
                        publicationApproved = true
                    } else {
                        publicationApproved = false
                    }
                }
            }
        }

        stage('Publish Artifacts') {
            when {
                expression {
                    publicationApproved
                }
            }
        
            steps {
                archiveArtifacts artifacts: 'target/*.war',
                                   fingerprint: true
            }
        
            post {
                success {
                    slackSend(
                        message: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER} - Artifact published successfully."
                    )
        
                    emailext(
                        subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                        body: """Build completed successfully.
        
        Job: ${env.JOB_NAME}
        Build: #${env.BUILD_NUMBER}
        Status: SUCCESS
        
        The WAR artifact was successfully published.""",
                        to: 'rahul96saini@gmail.com'
                    )
                }
        
                failure {
                    slackSend(
                        message: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER} - Artifact publication failed."
                    )
        
                    emailext(
                        subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                        body: """Build failed.
        
        Job: ${env.JOB_NAME}
        Build: #${env.BUILD_NUMBER}
        Status: FAILED
        
        Artifact publication failed. Please check the Jenkins console output.""",
                        to: 'rahul96saini@gmail.com'
                    )
                }
            }
        }
    }
}
