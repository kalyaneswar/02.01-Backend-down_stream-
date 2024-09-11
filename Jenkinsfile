// Downstream Pipeline: Receives the appVersion parameter, 
// downloads the artifact from Nexus, and deploys it to the target server.

pipeline {
    agent {
        label 'AGENT-1' // Use a different agent if needed for CD
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }

    parameters {
        string(name: 'appVersion', defaultValue: '', description: 'Version of the application to deploy')
    }

    environment {
        JENKINS_URL = 'http://jenkins.kalyaneswar.online:8080/'
        NEXUS_URL = 'http://nexus.kalyaneswar.online:8081'
        TARGET_SERVER = '12.22.33.123'
        SSH_USER = 'deployuser'
        SSH_KEY_ID = 'ssh-key-id'
        ARTIFACT_ID = 'backend'
        ARTIFACT_EXTENSION = 'zip'
        REPO_NAME = 'backend'
    }

    stages {
        stage('Download Artifact from Nexus') {
            steps {
                script {
                    def artifactFile = "backend-${params.appVersion}.zip"

                    nexusArtifactDownloader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${NEXUS_URL}",
                        groupId: 'com.expense',
                        artifactId: "${ARTIFACT_ID}",
                        version: "${params.appVersion}",
                        repository: "${REPO_NAME}",
                        credentialsId: 'nexus-auth',
                        target: artifactFile
                    )

                    echo "Downloaded artifact: ${artifactFile}"
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    // Transfer artifact to the target server
                    sh """
                        scp -i ~/.ssh/${SSH_KEY_ID} backend-${params.appVersion}.zip ${SSH_USER}@${TARGET_SERVER}:~/deploy/
                    """
                    
                    // Deploy the artifact on the target server
                    sh """
                        ssh -i ~/.ssh/${SSH_KEY_ID} ${SSH_USER}@${TARGET_SERVER} << EOF
                        cd ~/deploy
                        unzip -o backend-${params.appVersion}.zip -d /opt/app/
                        # Add any other deployment commands you need here
                        EOF
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            deleteDir()
        }
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
