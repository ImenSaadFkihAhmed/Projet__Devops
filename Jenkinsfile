#!/usr/bin/env groovy

def gv

pipeline {
    agent any

    environment {
        IMAGE_VERSION = "latest"
    }

    

    stages {
        stage("Initialize") {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }

        stage("Compile") {
            steps {
                script {
                    gv.compile()
                }
            }
        }

        stage("Build") {
            steps {
                script {
                    gv.buildJar()
                }
            }
        }

        stage("Test") {
            parallel {

                stage("Unit Tests") {
                    steps {
                        script {
                            gv.test()
                        }
                    }
                }

                stage("Dependency-Check") {
                    steps {
                        script {
                            gv.dependencyCheck()
                        }
                    }
                }
 stage("SonarQube Analysis") {
                    steps {
                         withSonarQubeEnv('SonarQube') {
                        sh "mvn verify sonar:sonar -Dsonar.projectKey=Projet-Devops -Dsonar.projectName=Projet-Devops -Dsonar.host.url=http://192.168.182.135:9000"
                }
                            
                
                        
                    }
                }

            }
        }

        stage("Publish Docker Image to Docker Hub") {
            steps {
                script {
                    gv.buildImage()
                }
            }
        }

        stage("Trivy Scan") {
            steps {
                script {
                    gv.trivyScan()
                }
            }
        }

        stage("JMeter Tests") {
            steps {
                script {
                    gv.jmeterTests()
                }
            }
        }

        stage("Deploy to Minikube Cluster") {
            steps {
                script {
                    gv.deploy()
                }
            }
        }

    }

    post {
        always {
            junit "target/surefire-reports/*.xml"
            emailext body: '''$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS: Check console output at $BUILD_URL to view the results.''',
                    subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', to: 'charradoumayma1@gmail.com'
        }
    }

}
