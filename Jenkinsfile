// Jenkins Pipeline - swift-5.6-release-debian-11-arm32v7
pipeline {
   agent { label 'arm64' }

   environment {
        def DATE = sh(script: "echo `date +%Y-%m-%d`", returnStdout: true).trim()
        SWIFT_BRANCH = 'release/5.6'
        SWIFT_SCHEME = 'release/5.6'
        SWIFT_VERSION = '5.6-DEVELOPMENT-SNAPSHOT'
        DOCKER_IMAGE = 'swiftarm/ci-build:arm32v7_debian_11'
        CONTAINER = 'swift-5.6-dev-debian-11-arm32v7'
        OS = 'debian'
        OS_VERSION = 'bulleye'
        ARCH = 'armhf'
        WORK_DIR = '/home/build-user'
   }
   stages {
      stage('Clean Workspace') {
         steps {
            echo 'Cleaning Workspace'
            cleanWs()
            script {
               currentBuild.displayName = "swift-${SWIFT_VERSION}-${DATE}-a"
            }
            sh 'mkdir output'
         }
      }
      stage('Checkout Swift') {
         steps {
            echo "Clone swift: ${SWIFT_BRANCH}"
            sh "git clone https://github.com/apple/swift.git"
            }
      }
      stage('Update Checkout') {
         steps {
            echo "scheme${SWIFT_SCHEME}: cloning supporting repos"
            sh "./swift/utils/update-checkout --clone --scheme ${SWIFT_SCHEME}"
         }
      }
      stage('Apply Patches') {
         steps {
            echo 'Apply Patches'
            dir('swift-docc') {
               echo "patch fix for python 3.9 +"
               sh 'wget https://github.com/apple/swift-docc/pull/73.patch'
               sh 'git apply 73.patch'
            }
            dir('swift') {
               echo "add swiftlang-min preset"
               sh 'wget https://raw.githubusercontent.com/futurejones/ci-swiftlang/main/patches/swift-5.6/swiftlang-min.patch'
               sh 'git apply swiftlang-min.patch'
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
               --platform linux/arm/v7 \
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
               --preset buildbot_linux,swiftlang-min -j70 \
               install_destdir=${WORK_DIR}/swift-install \
               installable_package=${WORK_DIR}/output/swiftlang-${SWIFT_VERSION}-${DATE}-a-${ARCH}-${OS}-${OS_VERSION}.tar.gz'"
            }
      }
      stage('Archive') {
         steps {
            echo 'Archive Build'
            sh "docker cp ${CONTAINER}:${WORK_DIR}/output/swiftlang-${SWIFT_VERSION}-${DATE}-a-${ARCH}-${OS}-${OS_VERSION}.tar.gz output/"
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