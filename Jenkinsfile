pipeline {
    agent {label 'arm64'}

    environment {
        DOCKER_IMAGE = 'swiftarm/swift:5.5.3-debian-bullseye'
        CONTAINER = 'helloswift'
        OS = 'Debian'
        OS_VERSION = 'bullseye'
    }

    stages {
        stage('Start Container') {
            steps {
                sh "docker pull ${DOCKER_IMAGE}"
                // remove container if exists
                sh "docker rm -f ${CONTAINER} || true"
                // start container detached and keep running
                sh "docker run -d \
                --cap-add=SYS_PTRACE \
                --security-opt seccomp=unconfined \
                --name ${CONTAINER} \
                ${DOCKER_IMAGE} \
                tail -f /dev/null"
            }
        }
        stage('Swift Version Check') {
            steps {
                sh "docker exec ${CONTAINER} swift --version"
            }
        }
        stage('Create Project Directory') {
            steps {
                sh "docker exec ${CONTAINER} mkdir helloswift"
            }
        }
        stage('Initialize Project') {
            steps {
                // -w sets working directory to the project directory
                sh 'docker exec -w /helloswift ${CONTAINER} swift package init --type executable'
            }
        }
        stage('Run Project') {
            steps {
                sh 'docker exec -w /helloswift ${CONTAINER} swift run'
            }
        }
        stage('Test Project') {
            steps {
                sh 'docker exec -w /helloswift ${CONTAINER} swift test'
            }
        }
        stage('Clean Up Docker') {
            steps {
                sh 'docker stop ${CONTAINER}'
                sh 'docker rm -f ${CONTAINER}'
            }
        }
    }
}