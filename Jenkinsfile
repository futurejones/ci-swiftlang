// Jenkins Pipeline - swift-main-ubuntu-24.04
pipeline {
   agent { label 'arm64' }

   environment {
        def DATE = sh(script: "echo `date +%Y-%m-%d`", returnStdout: true).trim()
        SWIFT_BRANCH = 'main'
        SWIFT_TAG = 'swift-main-dev'
        SWIFT_VERSION = 'main'
        ITERATION = '01'
        ARCHIVE_NAME = 'swiftlang-main-ubuntu-24.04'
        RELEASE = 'dev'
        DOCKER_IMAGE = 'swiftarm/ci-build:ubuntu_noble_swift'
        CONTAINER = 'swift-main-ubuntu-noble'
        OS = 'ubuntu'
        OS_VERSION = 'noble'
        WORK_DIR = '/home/build-user'
        ARCH = 'arm64'
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
            sh "./swift/utils/update-checkout --clone --scheme ${SWIFT_BRANCH}"
         }
      }
      stage('Apply Patches') {
         steps {
            echo 'no patches to apply'
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
