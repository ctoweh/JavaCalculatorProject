pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Install tools') {
            agent {
                label 'ansible-master'
                steps {
                    echo 'Installing the requirements'
                    ansiblePlaybook(
                        playbook: '01.install.yml',
                        inventory: 'hosts.ini'
                    )
                }
            }
        }
        stage('Build') {
            agent {
                label 'ansible-master'
                steps {
                    echo 'Building the app'
                    ansiblePlaybook(
                        playbook: '02.build.yml',
                        inventory: 'hosts.ini'
                    )
                }
            }
        }
        stage('Test') {
            agent {
                label 'ansible-master'
                steps {
                    echo 'Testing the app'
                    ansiblePlaybook(
                        playbook: '03.install.yml',
                        inventory: 'hosts.ini'
                    )
                }
            }
        }
        stage('Deploy') {
            agent {
                label 'ansible-master'
                steps {
                    echo 'Copy .war files to the deploy servers'
                    ansiblePlaybook(
                        playbook: '04.war-file.yml',
                        inventory: 'hosts.ini'
                    )
                }
            }
        }
        stage('Restart Tomcat') {
            agent {
                label 'ansible-master'
                steps {
                    echo 'Starting Tomcat'
                    ansiblePlaybook(
                        playbook: '05.restart.yml',
                        inventory: 'hosts.ini'
                    )
                }
            }
        }
        post {
            success {
                script {
                    // Send email for successful build
                    mail to: 'towehcorina@gmail.com',
                    subject: "Build Successful - ${currentBuild.fullDisplayName}",
                    body: "The build was successful.\n\nCheck console output at ${BUILD_URL}"
                }
            }
            failure {
                script {
                    // Send email for failed build
                    mail to: 'towehcorina@gmail.com',
                    subject: "Build Failed - ${currentBuild.fullDisplayName}",
                    body: "The build failed.\n\nCheck console output at ${BUILD_URL}"
                }
            }
        }
    }
}
