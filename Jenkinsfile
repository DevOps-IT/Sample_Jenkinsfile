pipeline {
    agent any
    parameters {
        string(defaultValue: "dev", description: '', name: 'ENV')
    }
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
        stage('Dev Deployment') {
            when {
                expression { params.ENV == 'dev' }
            }
            steps {
                echo "Hello, We are in DEV!"
            }
        }
    }
    post {
        always {
            cleanWs()
        }
        always {
           mail to: 'xs2vipin.bansal@gmail.com',
             subject: "Build Job: ${currentBuild.fullDisplayName}",
             body: "This is ${env.BUILD_URL}"
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
