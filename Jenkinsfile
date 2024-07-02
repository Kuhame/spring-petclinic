pipeline {
    agent {
        label "docker-slave"
    }
    environment {
        DISCORD_WEBHOOK_URL = "https://discord.com/api/webhooks/1256231536332902492/p0Q79BwJUTDIxpNuDDzE9cRDbF_s-LWvnX5TWYbrfGKOVZH76eR3bQoF_kcjShLmb2Yr"
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the Git repository
                git url: 'https://github.com/spring-projects/spring-petclinic.git', branch: 'main'
            }
        }
        stage('Test') {
            steps {
                // Execute unit tests
                sh './mvnw test'
            }
        }
        stage('Build') {
            steps {
                // Compile the project with Maven
                sh './mvnw package'
            }
        }
        stage('Deploy') {
            steps {
                sshagent(credentials: ['docker-agent']) {
                    // Create the directory on the server
                    sh 'ssh -o StrictHostKeyChecking=no vagrant@192.168.33.10 "mkdir -p /home/vagrant/apps/"'
                    
                    // Copy the JAR file to the server
                    sh 'scp -o StrictHostKeyChecking=no target/*.jar vagrant@192.168.33.10:/home/vagrant/apps/'
                    
                    // Run the JAR file on the server
                    sh 'ssh -o StrictHostKeyChecking=no vagrant@192.168.33.10 "nohup java -jar /home/vagrant/apps/*.jar > /home/vagrant/apps/app.log 2>&1 &"'
                }
            }
        }
        stage('Wait for Server') {
            steps {
                // Wait for the server to start
                sleep(time: 30, unit: 'SECONDS')
            }
        }
        stage('Check Homepage') {
            steps {
                script {
                    def response = sh(script: 'curl -o /dev/null -s -w "%{http_code}" http://192.168.33.10:8080', returnStdout: true).trim()
                    if (response != '200') {
                        error("Homepage did not return status 200. Actual status: ${response}")
                    }
                }
            }
        }
    }
    post {
        success {
            discordSend(
                webhookURL: DISCORD_WEBHOOK_URL,
                title: "${env.JOB_NAME} - Jenkins build success",
                link: env.BUILD_URL,
                result: "SUCCESS",
            )
        }
        unsuccessful {
            discordSend(
                webhookURL: DISCORD_WEBHOOK_URL,
                title: "${env.JOB_NAME} - Jenkins build failure",
                link: env.BUILD_URL,
                result: "FAILURE"
            )
        }
    }
}
