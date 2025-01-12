pipeline {
    agent {
        docker {
            image 'maven:3.6.3-jdk-11-slim'
        }

    }
    stages {
        stage('build') {
            agent {
                docker {
                    image 'maven:3.6.3-jdk-11-slim'
                }

            }
            steps {
                echo 'compile sysfoo app'
                sh 'mvn compile'
            }
        }

        stage('test') {
            agent {
                docker {
                    image 'maven:3.6.3-jdk-11-slim'
                }

            }
            steps {
                echo 'running unit tests'
                sh 'mvn clean test'
            }
        }

        stage('Deploy to Dev') {
            when {
                beforeAgent true
                branch 'master'
            }
            agent any
            steps {
                echo 'Deploying to Dev Compose'
                sh 'docker-compose up -d'
            }
        }

        stage('package') {
            when {
                beforeAgent true
                branch 'master'
            }
            agent any
            steps {
                echo 'packaging app to generate artifacts'
                sh 'mvn package -DskipTests'
                archiveArtifacts 'target/*.war'
            }
        }

        stage('Docker BnP.') {
            when {
                beforeAgent true
                branch 'master'
            }
            agent any
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                        def dockerImage = docker.build("bhim12/sysfoo:v${env.BUILD_ID}", "./")
                        dockerImage.push()
                        dockerImage.push("latest")
                        dockerImage.push("dev")
                    }
                }

            }
        }

    }
    tools {
        maven 'Maven 3.6.3'
    }
    post {
        always {
            echo 'This pipeline is completed..'
        }

    }
}
