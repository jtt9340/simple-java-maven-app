pipeline {
    agent {
        docker {
            // Downloads the maven:3.9.0-eclipse-temurin-11 Docker image (if it's not already downloaded) and runs this
            // image as a separate container. This means that the Maven container becomes the agent that Jenkins uses
            // to run the Pipeline project. However, this container is short-lived -- its lifespan is only that of the
            // duration of the Pipeline's execution.
            image 'maven:3.9.0-eclipse-temurin-11'

            // Creates a mapping between the /root/.m2 directory in the short-lived Maven Docker container and that
            // of the Docker host's filesystem.
            args '-v /root/.m2:/root/.m2'
        }
    }

    options {
        // From the Jenkins documentation: "skips stages once the build status has gone to UNSTABLE"
        skipStagesAfterUnstable()
    }

    stages {
        // Each call to 'stage' defines a stage that appears in the Jenkins UI
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }

            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}
