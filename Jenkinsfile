pipeline {
    agent {
        label "docker-slave"
    }

    stages {
        stage('Checkout') {
            steps {
                // Cloner le dépôt Git
                git url: 'https://github.com/Kuhame/spring-petclinic.git', branch: 'main'
            }
        }
        stage('Test') {
            steps {
                // Exécuter les tests unitaires
                sh './mvnw test'
            }
        }
        stage('Build') {
            steps {
                // Compiler le projet avec Maven
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

    }
    post {
        success {
            // Actions à effectuer en cas de succès
            echo 'Build succeeded!'
        }
        failure {
            // Actions à effectuer en cas d'échec
            echo 'Build failed!'
        }
    }
}
