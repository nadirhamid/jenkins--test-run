pipeline {

    environment {

        registry = "matrixnad/jenkins-docker"

        registryCredential = 'dockerhub_id'

        dockerImage = 'jenkins-docker'

    }

    agent any

    stages {

        stage('Cloning our Git') {

            steps {

                git 'https://github.com/nadirhamid/jenkins-test-run.git'

            }

        }

        stage('Building our image') {

            steps {

                script {

                    dockerImage = docker.build registry + ":$BUILD_NUMBER"

                }

            }

        }

        stage('Deploy our image') {

            steps {

                script {

                    docker.withRegistry( '', registryCredential ) { 

                        dockerImage.push()
                    }


                }

            }

        }

        stage('Cleaning up') {

            steps {

                sh "docker rmi $registry:$BUILD_NUMBER"

            }

        }

    }

}