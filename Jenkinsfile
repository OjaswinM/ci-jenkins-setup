pipeline {
    agent any
    
    
    parameters {
        string(
            name: 'label',
            description: 'Short name to easily identify the run',
            defaultValue: 'unlabelled'
        )
        string(
            name: 'kernelrepo',
            description: 'The repo where linux should be fetched from. Please note that this should be public. ',
            defaultValue: 'https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git'
        )
        string(
            name: 'branch',
            description: 'Branch in the repo',
            defaultValue: 'master'
        )
        choice(
            name: 'filesystem', 
            choices: ['ext4', 'xfs', 'btrfs'], 
            description: 'Filesystem type for testing'
        )
        string(
            name: 'config', 
            description: 'avocado misc test config to pass to xfstests.py test. Note that the config name should match exactly to the yaml files present for the corresponding FS here: https://github.com/OjaswinM/avocado-misc-tests/tree/master/fs/xfstests.py.data',
            defaultValue: 'ci.yaml'
        )
    }
    
    environment {
        BUILD_WS = "workspace_${env.BUILD_NUMBER}"
        // BUILD_WS = "workspace_tmp"
	
	// Used to ssh into the machine where logs are stored for dashboard
        remote_host = "localhost"
	// Used webserver port in the remote_host
        remote_port = "3001"
	// URL to use to send test results. This is based on the ci-dashboard hosted here:
	// https://github.com/OjaswinM/ci-dashboard
        dashboard_insert_url = "http://localhost:3001/api/insert-test-run"
    }
    stages {
        stage('Set Description') {
            when {
                expression { params.LABEL != 'unlabelled' }
            }
            steps {
                script {
                    currentBuild.description = params.label
                }
            }
        }
        stage('Prepare Script') {
            steps {
                dir("${BUILD_WS}") {
                    sh '''
                        rm -rf fs-ci-misc-scripts
                        git clone "https://github.com/OjaswinM/fs-ci-misc-scripts.git"
                    '''
                }
            }
        }
        stage('Build Boot Test') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dashboard-ssh-login',
                        usernameVariable: 'remote_user',
                        passwordVariable: 'remote_pass'
                    )
                ]) {
                    dir("${BUILD_WS}/fs-ci-misc-scripts") {
                        sh """
                            set +x
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
    
    post {
        always {
            archiveArtifacts artifacts: "${BUILD_WS}/fs-ci-misc-scripts/output/run/output/**", fingerprint: true, allowEmptyArchive: true
        }
    }
}

