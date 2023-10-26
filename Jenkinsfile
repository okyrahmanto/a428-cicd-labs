pipeline {
    agent {
        docker {
            image 'node:16-buster-slim'
            args '-p 3000:3000'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input(
                        id: 'userInput', message: 'Lanjutkan ke tahap Deploy?',
                        parameters: [choice(name: 'ACTION', choices: 'Proceed\nAbort', description: 'Pilih tindakan')],
                        submitter: currentBuild.currentResult
                    )
                    if (userInput == 'Proceed') {
                        echo 'Melanjutkan eksekusi pipeline ke tahap Deploy'
                    } else if (userInput == 'Abort') {
                        error 'Eksekusi pipeline dihentikan oleh pengguna'
                    }
                }
            }
        }
        stage('Deploy') {
            when {
                expression {
                    return env.userInput == 'Proceed'
                }
            }
            steps {
                sh './jenkins/scripts/deliver.sh' 
                sh 'sleep 60' 
                // input message: 'Sudah selesai menggunakan React App? (Klik "Proceed" untuk mengakhiri)' 
                sh './jenkins/scripts/kill.sh' 
            }
        }
    }
}