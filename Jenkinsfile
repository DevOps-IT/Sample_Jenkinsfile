pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                mvn 'clean install -DskipTests'
            }
        }
        stage('Unit Test') {
            steps {
                mvn 'test'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}

def mvn(def args) {
    def mvnHome = tool 'M3'
    def javaHome = tool 'JDK8'

    // --batch-mode : recommended in CI to inform maven to not run in interactive mode (less logs)
    // -V : strongly recommended in CI, will display the JDK and Maven versions in use.
    // -Dsurefire.useFile=false : useful in CI. Displays test errors in the logs directly 
    withEnv(["JAVA_HOME=${javaHome}", "PATH+MAVEN=${mvnHome}/bin:${env.JAVA_HOME}/bin"]) {
        sh "${mvnHome}/bin/mvn ${args} --batch-mode -V -U -e -Dsurefire.useFile=false"
    }
}
