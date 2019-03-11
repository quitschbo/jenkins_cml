pipeline {
   agent none
   options { checkoutToSubdirectory('trustme/cml') }
   stages {
      stage('Repo') {
         agent any
	 steps {
             sh 'repo init -u https://github.com/trustm3/trustme_main.git -b master -m ids-x86-yocto.xml'
             sh 'mkdir -p .repo/local_manifests'
             sh '''
                echo "<?xml version=\\\"1.0\\\" encoding=\\\"UTF-8\\\"?>" > .repo/local_manifests/jenkins.xml
                echo "<manifest><remove-project name=\\\"device_fraunhofer_common_cml\\\" /></manifest>" >> .repo/local_manifests/jenkins.xml
             '''
             sh 'repo sync -j8'
         }
      }
      stage('Build') {
         agent { dockerfile {
            dir 'trustme/build/yocto/docker'
            args '--entrypoint=\'\''
         } }
         steps {
            sh '''
               export LC_ALL=en_US.UTF-8
               export LANG=en_US.UTF-8
               export LANGUAGE=en_US.UTF-8
               . init_ws.sh out-yocto
               bitbake trustx-cml-initramfs multiconfig:container:trustx-core
            '''
         }
      }
      stage('Deploy') {
         agent { dockerfile {
            dir 'trustme/build/yocto/docker'
            args '--entrypoint=\'\''
         } }
         steps {
            sh '''
               . init_ws.sh out-yocto
               wic create -e trustx-cml-initramfs --no-fstab-update trustmeimage
            '''
         }
      }
   }
}
