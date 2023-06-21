// Jenkins Pipeline - swift-5.8-debian-sid-riscv64
pipeline {
   agent { label 'riscv64' }

   environment {
        def DATE = sh(script: "echo `date +%Y-%m-%d`", returnStdout: true).trim()
        SWIFT_BRANCH = 'release/5.8'
        SWIFT_SCHEME = 'release/5.8'
        SWIFT_VERSION = '5.8-DEVELOPMENT-SNAPSHOT'
        DOCKER_IMAGE = 'swiftarm/ci-build:debian_sid_riscv64'
        CONTAINER = 'swift-5.8-dev-debian-sid-riscv64'
        OS = 'debian'
        OS_VERSION = 'sid'
        ARCH = 'riscv64'
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
            dir('swift') {
               echo "apply swift patches"
               sh "wget https://github.com/swift-riscv/swift-riscv64/raw/main/patches/swift/5.8/add-riscv64-as-supported-architecture.patch"
               sh "git apply add-riscv64-as-supported-architecture.patch"
               sh "wget https://github.com/swift-riscv/swift-riscv64/raw/main/patches/swift/5.8/add-RISCV-llvm-target-to-build.patch"
               sh "git apply add-RISCV-llvm-target-to-build.patch"
               // sh "wget https://github.com/swift-riscv/swift-riscv64/raw/main/patches/release-5.8-branch/mno-relax.patch"
               // sh "git apply mno-relax.patch"
            }
            dir('llvm-project') {
               echo "apply llvm-project patches"
               sh "wget https://github.com/swift-riscv/swift-riscv64/raw/main/patches/release-5.8-branch/llvm-calling-conv-rscv.patch"
               sh "git apply llvm-calling-conv-rscv.patch"
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
               --preset buildbot_linux_riscv64,no_test \
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