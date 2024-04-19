pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Installations') {
            steps {
                echo 'Installations'
                script {
                    sh 'git pull /home/centos/java_web_app/'

                    // Install all dependencies using playbook
                    ansiblePlaybook(
                        playbook: '/home/centos/java_web_app/01.install.yml',
                        inventory: '/home/centos/java_web_app/hosts.ini'
                    )
                }
            }
        }
        stage('Build') {
            agent {
                label 'ansible-master'
            }
            steps {
                echo 'Building the app'
                ansiblePlaybook(
                    playbook: '02.build.yml',
                    inventory: 'hosts.ini'
                )
            }
        }
        stage('Deploy') {
            agent {
                label 'ansible-master'
            }
            steps {
                echo 'Deploying artifacts to Nexus'
                ansiblePlaybook(
                    playbook: '04.war-file.yml',
                    inventory: 'hosts.ini'
                )
            }
        }
        stage('Deploy to Servers') {
            agent {
                label 'ansible-master'
            }
            steps {
                echo 'Deploying artifacts to the deployment servers'
                ansiblePlaybook(
                    playbook: '04.war-file.yml',
                    inventory: 'hosts.ini',
                    extraVars: [
                        warfile: "${WORKSPACE}/warfile.json" // Adjust the path accordingly
                    ]
                )
            }
        }
        stage('Restart Tomcat') {
            agent {
                label 'ansible-master'
            }
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
