pipeline {
    agent { label "master"}
    stages {
        stage('Delete the workspace') {
            steps {
                sh 'pwd'
                sh 'ls -la'
                sh "sudo rm -rf $WORKSPACE/*"
            }
        }

        /* stage('Installing Habitat') {
            steps {
                script {
                    def exists = fileExists '/hab/'
                    if (exists) {
                        echo "Skipping Habitat install - already installed!"
                    } else {
                        sh 'sudo apt-get install -y curl'
                        sh 'curl https://raw.githubusercontent.com/habitat-sh/habitat/master/components/hab/install.sh | sudo bash'
                        sh 'useradd hab'
                        sh 'usermod -aG hab hab'
                    }
                }

            }
        } */

        stage('Loading Hardening Cookbook') {
            steps {
                git credentialsId: 'repo_key', url: 'git@github.com:jasonnalewak/os-hardening.git'
            }
        }

        stage('Local Testing') {
            steps {
                script {
                    sh 'cookstyle .'
                    sh 'foodcritic . --tags ~FC070,FC078'
                }
            }
        }

/*         stage('Loading the Packer Hab Pkg') {
            steps {
                script {
                    sh 'sudo hab svc load core/packer'
                }
            }
        }

/*         stage('Loading the Web App Hab Package') {
            steps {
                script{
                    sh 'nohup sudo hab sup run $>/dev/null &'
                    sh 'sudo hab svc load jnalewak-hab-pub/mongodb-parks'
                    sh 'sudo hab svc load jnalewak-hab-pub/national-parks --bind database:mongodb-parks.default'
                }
            }
        }

        stage('Send Slack Notification') {
            steps{
                slackSend color: '#449FE0', message: "Jason Nalewak: Please approve ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.JOB_URL}|Open>)"
            }
            
        }

        stage('Wait for Input'){
            steps{
                input 'Proceed with build?'
            }
        } */
    }

    //post actions
    post {
        success {
        slackSend color: '#439FE0', message: "Build $JOB_NAME $BUILD_NUMBER was successful"
        }
        failure {
            echo "Build Failed"
            mail  body: "Build ${env.JOB_NAME} ${env.BUILD_NUMBER} failed. Please check the build at ${env.JOB_URL}", from: 'admin@acme.com', subject: 'Build Failure', to: 'jnalewak@chef.io'
        }
    }

}