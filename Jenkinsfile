// Jenkins Pipeline - swift-5.6-release-debian-11-arm32v7
pipeline {
   agent { label 'arm64' }

   environment {
        def DATE = sh(script: "echo `date +%Y-%m-%d`", returnStdout: true).trim()
        SWIFT_BRANCH = 'release/5.6'
        SWIFT_SCHEME = 'release/5.6'
        SWIFT_TAG = 'swift-5.6.1-RELEASE'
        SWIFT_VERSION = '5.6.1'
        DOCKER_IMAGE = 'swiftarm/ci-build:arm32v7_debian_11'
        CONTAINER = "swift-5.6-dev-debian-11-arm32v7-${BUILD_NUMBER}"
        OS = 'debian'
        OS_VERSION = 'bulleye'
        ARCH = 'armhf'
        WORK_DIR = '/home/build-user'
   }
   stages {
      stage('Clean Workspace') {
         steps {
            echo 'Cleaning Workspace'
            sh label: '', script: '''#!/bin/bash
            shopt -s extglob
            sudo rm -rf !("patches")'''
            script {
               currentBuild.displayName = "swift-${SWIFT_VERSION}-${DATE}"
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
            sh "./swift/utils/update-checkout --clone --tag ${SWIFT_TAG} --skip-repository icu --skip-repository cmake"
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
               sh 'git apply $WORKSPACE/patches/swiftlang-min.patch'
               echo "patch fix for AST errors"
               sh 'patch -p2 < $WORKSPACE/patches/swift-arm.patch'
            }
            dir('swift-corelibs-libdispatch') {
               echo "patch fix for benchmark errors"
               sh 'git apply $WORKSPACE/patches/benchmark.diff'
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
            sh "docker run \
               --rm \
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
               installable_package=${WORK_DIR}/output/swiftlang-${SWIFT_VERSION}-RELEASE-${ARCH}-${OS}-${OS_VERSION}.tar.gz'"
            }
      }
      stage('Archive') {
         steps {
            echo 'Archive Build'
            sh "docker cp ${CONTAINER}:${WORK_DIR}/output/swiftlang-${SWIFT_VERSION}-RELEASE-${ARCH}-${OS}-${OS_VERSION}.tar.gz output/"
            archiveArtifacts 'output/*.tar.gz'
         }
      }
      stage('Cleanup Docker') {
          steps {
              echo 'remove docker volume'
              sh "docker volume rm ${CONTAINER}"
          }
      }
   }
}
