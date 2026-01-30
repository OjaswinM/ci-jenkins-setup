pipeline {
    agent any
    environment {
        WORKSPACE = "workspace_${env.BUILD_NUMBER}"
    }

	parameters {
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

