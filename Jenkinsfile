pipeline {
    agent any
    environment {
        WORKSPACE = "workspace_${env.BUILD_NUMBER}"
    }
    stages {
         stage('Prepare Script') {
            steps {
                dir("$env.WORKSPACE") {
                    sh '''
                        if ! [[ -d fs-ci-misc-scripts ]]
                        then
                            git clone "https://github.com/OjaswinM/fs-ci-misc-scripts.git"
                        fi
                    '''
                }
            }
        }
        stage('Build Boot Test') {
            steps {
                dir("$env.WORKSPACE/fs-ci-misc-scripts") {
                    sh """
                        id
                        ./build-boot-script.sh \
                        ${params.kernelrepo} \
                        ${params.branch} \
                        ubuntu20.04-cloudimg-ppc64el.qcow2 \
                        ${params.filesystem}:${params.config}
                    """
                }
            }
        }
    }
}

