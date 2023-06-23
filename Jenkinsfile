// Jenkins Pipeline - swift-5.8-debian-unstable-riscv64-docker-cached
pipeline {
    agent { label 'swiftaltra' }
    
    environment {
        def DATE = sh(script: "echo `date +%Y-%m-%d`", returnStdout: true).trim()
        SWIFT_BRANCH = 'release/5.8'
        SWIFT_SCHEME = 'release/5.8'
        SWIFT_VERSION = '5.8-DEVELOPMENT-SNAPSHOT'
        DOCKER_IMAGE = 'swiftarm/ci-build:debian_unstable_riscv64'
        CONTAINER = 'riscv64-unstable'
        OS = 'debian'
        OS_VERSION = 'unstable'
        ARCH = 'riscv64'
        WORK_DIR = '/home/build-user'
        BUILD_CMD = './swift/utils/build-script'
        PRESET = '--preset=buildbot_linux_riscv64,no_test'
        DESTDIR = 'install_destdir=/home/build-user/install'
        PACKAGE = 'installable_package=/home/build-user/install/swift-5.8-riscv64.tar.gz'
   }

    stages {
        stage('Start Container') {
            steps {
                echo 'starting container'
                sh 'docker start ${CONTAINER}'
            }
        }
        stage('List Source') {
            steps {
                echo 'running command'
                sh 'docker exec -w ${WORK_DIR}/swift ${CONTAINER} ls'
            }
        }
        stage('Patch Swift') {
            steps {
                // Patch #1
                echo 'SWIFT PATCH #1'
                script {
                    env.URL = 'https://github.com/swift-riscv/swift-riscv64/raw/main/patches/swift/5.8/add-riscv64-as-supported-architecture.patch'
                    env.PATCH = 'add-riscv64-as-supported-architecture.patch'
                }
                echo 'download patch'
                sh "docker exec -w ${WORK_DIR}/swift ${CONTAINER} /bin/bash -c '[ -e '${env.PATCH}' ] && echo file exists || wget ${env.URL}'"
                echo 'apply patch'
                sh "docker exec -w ${WORK_DIR}/swift ${CONTAINER} /bin/bash -c 'git apply --check -q ${env.PATCH} && git apply ${env.PATCH} || echo patch already applied'"
                //
                // Patch #2
                echo 'SWIFT PATCH #2'
                script {
                    env.URL = 'https://github.com/swift-riscv/swift-riscv64/raw/main/patches/swift/5.8/add-RISCV-llvm-target-to-build.patch'
                    env.PATCH = 'add-RISCV-llvm-target-to-build.patch'
                }
                echo 'download patch'
                sh "docker exec -w ${WORK_DIR}/swift ${CONTAINER} /bin/bash -c '[ -e '${env.PATCH}' ] && echo file exists || wget ${env.URL}'"
                echo 'apply patch'
                sh "docker exec -w ${WORK_DIR}/swift ${CONTAINER} /bin/bash -c 'git apply --check -q ${env.PATCH} && git apply ${env.PATCH} || echo patch already applied'"
                //
                // Patch #3
                echo 'SWIFT PATCH #3'
                script {
                    env.URL = 'https://github.com/swift-riscv/swift-riscv64/raw/main/patches/swift/5.8/use-ld-linker.patch'
                    env.PATCH = 'use-ld-linker.patch'
                }
                echo 'download patch'
                sh "docker exec -w ${WORK_DIR}/swift ${CONTAINER} /bin/bash -c '[ -e '${env.PATCH}' ] && echo file exists || wget ${env.URL}'"
                echo 'apply patch'
                sh "docker exec -w ${WORK_DIR}/swift ${CONTAINER} /bin/bash -c 'git apply --check -q ${env.PATCH} && git apply ${env.PATCH} || echo patch already applied'"
                //
                // Patch #4
                echo 'SWIFT PATCH #4'
                script {
                    env.URL = 'https://github.com/swift-riscv/swift-riscv64/raw/main/patches/swift/5.8/mno-relax.patch'
                    env.PATCH = 'mno-relax.patch'
                }
                echo 'download patch'
                sh "docker exec -w ${WORK_DIR}/swift ${CONTAINER} /bin/bash -c '[ -e '${env.PATCH}' ] && echo file exists || wget ${env.URL}'"
                echo 'apply patch'
                sh "docker exec -w ${WORK_DIR}/swift ${CONTAINER} /bin/bash -c 'git apply --check -q ${env.PATCH} && git apply ${env.PATCH} || echo patch already applied'"
                //
                // Patch #5
                echo 'SWIFT PATCH #5'
                script {
                    env.URL = 'https://github.com/swift-riscv/swift-riscv64/raw/main/patches/swift/5.8/buildbot-linux-riscv64.patch'
                    env.PATCH = 'buildbot-linux-riscv64.patch'
                }
                echo 'download patch'
                sh "docker exec -w ${WORK_DIR}/swift ${CONTAINER} /bin/bash -c '[ -e '${env.PATCH}' ] && echo file exists || wget ${env.URL}'"
                echo 'apply patch'
                sh "docker exec -w ${WORK_DIR}/swift ${CONTAINER} /bin/bash -c 'git apply --check -q ${env.PATCH} && git apply ${env.PATCH} || echo patch already applied'"
            }
        }
        stage('Patch LLVM-Project') {
            steps {
                // Patch #1
                echo 'LLVM PROJECT PATCH #1'
                script {
                    env.URL = 'https://github.com/swift-riscv/swift-riscv64/raw/main/patches/llvm-project/5.8/llvm-calling-conv-rscv.patch'
                    env.PATCH = 'llvm-calling-conv-rscv.patch'
                }
                echo 'download patch'
                sh "docker exec -w ${WORK_DIR}/llvm-project ${CONTAINER} /bin/bash -c '[ -e '${env.PATCH}' ] && echo file exists || wget ${env.URL}'"
                echo 'apply patch'
                sh "docker exec -w ${WORK_DIR}/llvm-project ${CONTAINER} /bin/bash -c 'git apply --check -q ${env.PATCH} && git apply ${env.PATCH} || echo patch already applied'"
                //
            }
        }
        stage('Build Swift') {
            steps {
                echo 'building swift'
                sh 'docker exec ${CONTAINER} ${BUILD_CMD} ${PRESET} ${DESTDIR} ${PACKAGE}'
            }
        }
    }
}
