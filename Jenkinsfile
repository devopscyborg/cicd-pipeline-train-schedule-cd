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
        stage('DeployToStaging'){
            when{
                branch 'master'
            }
            steps{
                withCredetials([usernamePassword(credentialsId: 'staging_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]){
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                        sshPublisherDesc(
                            configName: 'staging',
                            sshCredentials: [
                            username: "$USERNAME",
                            encryptedPassphrase: "$USERPASS"
                            ],
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'dist/trainSchedule.zip',
                                    removePrefix: 'dist/',
                                    remoteDirectory: '/temp',
                                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /temp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start'
                                   )
                               ]
                           )
                       ]
                    )
                }
            }
        }
        stage('DeployToProduction'){
            when{
                branch 'master'
            }
            steps{
                input 'Does the staging enviroment look OK?'
                milestone(1)
                withCredetials([usernamePassword(credentialsId: 'staging_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]){
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                        sshPublisherDesc(
                            configName: 'staging',
                            sshCredentials: [
                            username: "$USERNAME",
                            encryptedPassphrase: "$USERPASS"
                            ],
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'dist/trainSchedule.zip',
                                    removePrefix: 'dist/',
                                    remoteDirectory: '/temp',
                                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /temp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start'
                                   )
                               ]
                           )
                       ]
                    )
                }
            }
        }
    
    }
}
