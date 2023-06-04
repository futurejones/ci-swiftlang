// Jenkins Pipeline - swift-5.9-ubuntu-lunar-riscv64
pipeline {
   agent { label 'visionfive2' }

   environment {
        def DATE = sh(script: "echo `date +%Y-%m-%d`", returnStdout: true).trim()
        SWIFT_BRANCH = 'release/5.9'
        SWIFT_SCHEME = 'release/5.9'
        SWIFT_VERSION = '5.9-DEVELOPMENT-SNAPSHOT'
        OS = 'ubuntu'
        OS_VERSION = 'lunar'
        ARCH = 'riscv64'
   }
   stages {
      stage('Clean Workspace') {
         steps {
            echo 'Cleaning Workspace'
            cleanWs()
            script {
               currentBuild.displayName = "swift-${SWIFT_VERSION}-${DATE}-a"
            }
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
               echo "no swift patches"
               sh "wget https://github.com/swift-riscv/swift-riscv64/raw/main/patches/release-5.8-branch/add-riscv64-to-host-targets.patch"
               sh "git apply add-riscv64-to-host-targets.patch"
               sh "wget https://github.com/swift-riscv/swift-riscv64/raw/main/patches/release-5.8-branch/add-RISCV-llvm-target-to-build.patch"
               sh "git apply add-RISCV-llvm-target-to-build.patch"
            }
            dir('llvm-project') {
               echo "no llvm-project patches"
            }
         }
      }
      stage('Build Swift') {
         steps {
            echo 'Building toolchain'
            sh "./swift/utils/build-script \
               --preset buildbot_linux,no_test \
               install_destdir=${WORKSPACE}/swift-install \
               installable_package=${WORKSPACE}/output/swiftlang-${SWIFT_VERSION}-${DATE}-a-${ARCH}-${OS}-${OS_VERSION}.tar.gz -n"
            }
      }
      stage('Archive') {
         steps {
            echo 'Archive Build'
            archiveArtifacts 'output/*.tar.gz'
         }
      }
   }
}