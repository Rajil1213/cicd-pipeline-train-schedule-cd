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
        stage('DeployToStaging') {
            steps {
                echo 'Building on the staging server first for testing'
                withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'password', usernameVariable: 'username')]) {
                    sshPublisher failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging', 
                                sshCredentials: [
                                    encryptedPassphrase: $password, 
                                    key: '', 
                                    keyPath: '', 
                                    username: $username],
                                transfers: [
                                    sshTransfer(
                                        excludes: '', 
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && sudo rm -rf /opt/train-schedule && sudo unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule', 
                                        execTimeout: 120000, 
                                        flatten: false, 
                                        makeEmptyDirs: false, 
                                        noDefaultExcludes: false, 
                                        patternSeparator: '[, ]+', 
                                        remoteDirectory: '/tmp', 
                                        remoteDirectorySDF: false, 
                                        removePrefix: 'dist', 
                                        sourceFiles: 'dist/trainSchedule.zip')], 
                                usePromotionTimestamp: false,
                                useWorkspaceInPromotion: false, 
                                verbose: false)]
                }
            }
        }
        stage('DeployToProduction') {
            steps {
                input 'Proceed to build in production?'
                sshPublisher failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging', 
                                sshCredentials: [
                                    encryptedPassphrase: '{AQAAABAAAAAQtZl73Q0cdiOR5myHRwcaY3xyBRI+pP2b9si+f/AF3FM=}', 
                                    key: '', 
                                    keyPath: '', 
                                    username: 'deploy'],
                                transfers: [
                                    sshTransfer(
                                        excludes: '', 
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && sudo rm -rf /opt/train-schedule && sudo unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule', 
                                        execTimeout: 120000, 
                                        flatten: false, 
                                        makeEmptyDirs: false, 
                                        noDefaultExcludes: false, 
                                        patternSeparator: '[, ]+', 
                                        remoteDirectory: '/tmp', 
                                        remoteDirectorySDF: false, 
                                        removePrefix: 'dist', 
                                        sourceFiles: 'dist/trainSchedule.zip')], 
                                usePromotionTimestamp: false,
                                useWorkspaceInPromotion: false, 
                                verbose: false)]
                }
            }
        }
    }
}
