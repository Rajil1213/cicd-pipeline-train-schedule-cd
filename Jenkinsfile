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
                sshPublisher 
                    failOnError: true,
                    publishers: [
                        sshPublisherDesc(
                            configName: 'staging', 
                            transfers: [
                                sshTransfer(
                                    excludes: '', 
                                    execCommand: 'sudo systemctl stop train-schedule && sudo rm -rf /opt/train-schedule && sudo unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo systemctl start train-schedule', 
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
        stage('DeployToProduction') {
            steps {
                input 'Proceed to build in production?'
                sshPublisher failOnError: true, publishers: [sshPublisherDesc(configName: 'prod', transfers: [sshTransfer(excludes: '', execCommand: 'sudo systemctl stop train-schedule && sudo rm -rf /opt/train-schedule && sudo unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo systemctl start train-schedule', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/tmp', remoteDirectorySDF: false, removePrefix: 'dist', sourceFiles: 'dist/trainSchedule.zip')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)]
            }
        }
    }
}
