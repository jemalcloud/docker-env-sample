pipeline {
    agent { label 'CloudSheger' }

    environment {
    //registry = "YourDockerhubAccount/YourRepository"
    REGISTRY = "https://hub.docker.com/repositories/cloudsheger/"
    //registryCredential = 'dockerhub_id'
    //registryCredential = 'DockerID'
    REGISTRY_CREDENTIAL = 'DockerID'
    dockerImage = ''
    }
    triggers { pollSCM('') }

    options {
        buildDiscarder(logRotator(
                numToKeepStr: '10',
                daysToKeepStr: '30',
                artifactDaysToKeepStr: '30',
                artifactNumToKeepStr: '3'
            )
        )
        disableConcurrentBuilds()
        timeout(time: 10, unit: 'MINUTES')
    }

    stages {
        stage('SCM') {
            steps {
                checkout scm
            }
        }
        stage('dockerize') {
            steps {
                script {
                    docker.withRegistry(${env.REGISTRY},${env.REGISTRY_CREDENTIAL}) {
                        stage('build') {
                            image = docker.build("DOCKER-ENV-AMI:${env.BUILD_ID}")
                        }
                        stage('deploy') {
                            image.push()
                            image.push('latest')
                            if (env.TAG_NAME) {
                                image.push("${env.TAG_NAME}")
                            }
                        }
                    }
                }
            }
        }
    }

    post {
      always {
        cleanWs(disableDeferredWipeout: true)
      }
    }
}
