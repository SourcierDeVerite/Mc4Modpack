pipeline {
    agent any

    stages {
        stage("Clone repo") {
            steps {
                git url: "https://github.com/SourcierDeVerite/Mc4Modpack.git", branch: "master"
            }
        }

        stage("Deploy to Nginx") {
            steps {
                sh """
                rm -rf /nginx/html/*
                cp -r * /nginx/html
                """
            }
        }

        stage("Run Packwiz") {
            steps {
                sh """
                docker exec mc-4 java -jar packwiz-installer-bootstrap.jar -g https://nginx.sourcierdeverite.xyz/pack.toml
                """
            }
        }

        stage("Warn Players if needed") {
            steps {
                script {
                    def restartDelay = 10

                    def players = sh(
                        script: "docker exec mc-4 rcon-cli list | awk '{print \$3}'",
                        returnStdout: true
                    ).trim().toInteger()

                    if (players > 0) {
                        sh """
                        docker exec mc-4 rcon-cli "say Server will restart in ${restartDelay} seconds!"
                        """
                        sleep(time: restartDelay, unit: "SECONDS")
                    }
                }
            }
        }

        stage("Restart Server") {
            steps {
                sh "docker restart mc-4"
            }
        }
    }
}