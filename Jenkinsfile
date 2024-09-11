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
        NEXUS_URL = 'nexus.kalyaneswar.online:8081'
        TARGET_SERVER = '54.174.150.47'
        SSH_USER = 'ec2-user'
        SSH_KEY_ID = 'id_rsa'
        ARTIFACT_ID = 'backend'
        ARTIFACT_EXTENSION = 'zip'
        REPO_NAME = 'backend'
    }

    stages {
            // stage('Download Artifact from Nexus') {
            //     steps {
            //         script {
            //             echo "Downloading Artifacts from Nexus"
            //             def artifactFile = "backend-${params.appVersion}.zip"

            //             nexusArtifactDownloader(
            //                 nexusVersion: 'nexus3',
            //                 protocol: 'http',
            //                 nexusUrl: "${NEXUS_URL}",
            //                 groupId: 'com.expense',
            //                 artifactId: "${ARTIFACT_ID}",
            //                 version: "${params.appVersion}",
            //                 repository: "${REPO_NAME}",
            //                 credentialsId: 'nexus-auth',
            //                 target: artifactFile
            //             )

            //             echo "Downloaded artifact: ${artifactFile}"
            //         }
            //     }
            // }

            stage('Deploy to Server') {
                steps {
                    script {
                        // Transfer artifact to the target server
                    // scp -i ~/.ssh/${SSH_KEY_ID} backend-${params.appVersion}.zip ${SSH_USER}@${TARGET_SERVER}
                        sh """
                            hostname
                            pwd
                            ls -lrt
                           
                        """
                        
                        // Deploy the artifact on the target server
                        sh """
                            pwd

                            ssh -i ~/.ssh/${SSH_KEY_ID} ${SSH_USER}@${TARGET_SERVER} 
                            pwd
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
