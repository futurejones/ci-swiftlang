// Jenkins Pipeline - swift-5.10-ubuntu-23.04
pipeline {
   agent { label 'swiftaltra' }

   environment {
        def DATE = sh(script: "echo `date +%Y-%m-%d`", returnStdout: true).trim()
        SWIFT_BRANCH = 'release/5.10'
        SWIFT_TAG = 'swift-5.10-RELEASE'
        SWIFT_VERSION = '5.10'
        ITERATION = '01'
        ARCHIVE_NAME = 'swiftlang-5.10-debian-sid'
        RELEASE = 'release'
        DOCKER_IMAGE = 'swiftarm/ci-build:debian_sid_riscv64_cmake_v3.24.2'
        CONTAINER = 'swift-5.10-debian-sid-riscv64'
        OS = 'debian'
        OS_VERSION = 'sid'
        WORK_DIR = '/home/build-user'
        ARCH = 'riscv64'
    }
   stages {
      stage('Clean Workspace') {
         steps {
            echo 'Cleaning Workspace'
            cleanWs()
            script {
               currentBuild.displayName = "swift-${SWIFT_VERSION}-RELEASE-${ITERATION}"
            }
            sh 'mkdir output'
         }
      }
      stage('Checkout Swift') {
         steps {
            echo "Clone swift: ${SWIFT_BRANCH}"
            sh "git clone https://github.com/apple/swift.git"
            dir('swift'){
               sh "git checkout ${SWIFT_BRANCH}"
            }
            }
      }
      stage('Update Checkout') {
         steps {
            echo "tag=${SWIFT_BRANCH}: cloning supporting repos"
            sh "./swift/utils/update-checkout --clone --tag ${SWIFT_TAG}"
         }
      }
      stage('Apply Patches') {
         steps {
            dir('swift'){
               sh "wget https://raw.githubusercontent.com/swift-riscv/swift-riscv64/main/patches/swift/5.10/set-lld-linker-as-default-5.10-v2.patch"
               sh "git apply set-lld-linker-as-default-5.10-v2.patch"
               sh "wget https://raw.githubusercontent.com/swift-riscv/swift-riscv64/main/patches/swift/5.10/bootstrapping-off-5.10.patch"
               sh "git apply bootstrapping-off-5.10.patch"
               sh "wget https://raw.githubusercontent.com/swift-riscv/swift-riscv64/main/patches/swift/5.10/nostart-stop-gc-5.10.patch"
               sh "git apply nostart-stop-gc-5.10.patch"
            }
            dir('swift-driver'){
               sh "wget https://github.com/apple/swift-driver/pull/1545.patch"
               sh "git apply 1545.patch"
            }
         }
      }
      stage('Pull Docker Image') {
         steps {
            echo 'Getting Docker Image'
            sh "docker pull ${DOCKER_IMAGE}"
         }
      }
      stage('Build Swift') {
         steps {
            echo 'Building toolchain'
            sh "docker rm -f ${CONTAINER} || true"
            sh "docker volume rm ${CONTAINER} || true"
            sh "docker run \
               --platform linux/riscv64 \
               --cap-add=SYS_PTRACE \
               --security-opt seccomp=unconfined \
               --name ${CONTAINER} \
               -w ${WORK_DIR} \
               -v ${WORKSPACE}:/source \
               -v ${CONTAINER}:${WORK_DIR} \
               ${DOCKER_IMAGE} \
               /bin/bash -lc \
               'cp -r /source/* ${WORK_DIR}; \
               ./swift/utils/build-script \
               --preset buildbot_linux,no_assertions,no_test \
               install_destdir=${WORK_DIR}/swift-install \
               installable_package=${WORK_DIR}/output/${ARCHIVE_NAME}-${RELEASE}-${ARCH}-${ITERATION}.tar.gz'"
            }
      }
      stage('Archive') {
         steps {
            echo 'Archive Build'
            sh "docker cp ${CONTAINER}:${WORK_DIR}/output/${ARCHIVE_NAME}-${RELEASE}-${ARCH}-${ITERATION}.tar.gz output/"
            archiveArtifacts 'output/*.tar.gz'
         }
      }
      stage('Cleanup Docker') {
          steps {
              echo 'remove docker container'
              sh "docker rm -f ${CONTAINER}"
              sh "docker volume rm ${CONTAINER}"
          }
      }
   }
}
