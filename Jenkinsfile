pipeline {
    agent any
    parameters {
        string(defaultValue: "dev", description: '', name: 'ENV')
    }
    stages {
        stage('Build') {
            steps {
                mvn 'clean install' //-DskipTests'
            }
        }
        stage('Unit Test') {
            steps {
                mvn 'test'
            }
        }
        stage ('SonarQube') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'master') {
                        withSonarQubeEnv ('Sonar_TEST') {
                            sh 'mvn package -DskipTests -U sonar:sonar -Dsonar.branch.name=master -Dsonar.host.url="http://34.72.27.231:9000/" -Dsonar.login=sonarqube-token -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Dmaven.wagon.http.ssl.ignore.validity.dates=true'
                        }
                    } else if (env.GIT_BRANCH == 'development') {
                        withSonarQubeEnv ('SonarQube') {
                            sh 'mvn package -DskipTests -U sonar:sonar -Dsonar.branch.name=development -Dsonar.host.url=SONARQUBE_SERVER_URL -Dsonar.login=18b145db92099cd1ae7fc5514633ccf07ada9243 -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Dmaven.wagon.http.ssl.ignore.validity.dates=true'
                        }
                    } 
                }
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
            junit testResults: '**/target/surefire-reports/*.xml', allowEmptyResults: false
            cleanWs()
            //mail to: '',
             //subject: "Build Job: ${currentBuild.fullDisplayName}",
             //body: "This is ${env.BUILD_URL}"
            emailext body: 'Body of Mail: ${env.BUILD_URL}', recipientProviders: [requestor()], subject: 'Subject of Mail: ${currentBuild.fullDisplayName}', to: ''
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
