pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }    
        stage('Deploytostaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'staging-prod', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS' )])
                sshPublisher(
                    failOnError: true,
                    continueOnError: false,
                    publishers:[
                        sshPublisherDesc(
                            configName: 'Staging-Prod',
                            sshCredentials: [
                            username: "$USERNAME",
                            encryptedPassphrase: "$USERPASS"
                            ],
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'dist/trainSchedule.zip',
                                    removePrefix: 'dist/',
                                    removeDirectory: '/tmp',
                                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm-rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'                                    
                                )
                            ]
                        )
                    ]
                )

            }
        }
        stage('DeploytoProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Does staging look okay'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'staging-prod', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS' )])
                sshPublisher(
                    failOnError: true,
                    continueOnError: false,
                    publishers:[
                        sshPublisherDesc(
                            configName: 'Staging-Prod',
                            sshCredentials: [
                            username: "$USERNAME",
                            encryptedPassphrase: "$USERPASS"
                            ],
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'dist/trainSchedule.zip',
                                    removePrefix: 'dist/',
                                    removeDirectory: '/tmp',
                                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm-rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'                                    
                                )
                            ]
                        )
                    ]
                )
            }
        }             
    }
}
