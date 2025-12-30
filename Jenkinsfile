pipeline {
    agent {

        node {
            label 'agent-1'
        }
    }
    environment {
        COURSE = "Jenkins"
        ACC_ID = "347539192046"
        PROJECT = "roboshop"
        COMPONENT = "catalogue"
        appVersion = ""
    }
    options {
        timeout(time:10,unit: 'MINUTES')
        disableConcurrentBuilds()
    }
    
    //this is build section
    stages {
        stage('Read Version') {
            steps {
                script{
                    def packageJSON = readJSON file: 'package.json'
                    appVersion = packageJSON.version
                    echo "app version: ${appVersion}"
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                script{
                    sh """
                        npm install
                    """
                }
            }
        }
        stage('Build'){
            steps {
                script{
                    withAWS(region:'us-east-1',credentials:'AWS-Creds'){
                        sh """
                            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com
                            docker build -t ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .
                            docker images
                            docker push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}

                        """
                    }
                }
            }
        }
        stage('Test') {
            steps {
                script{
                    sh """
                        echo "Building"
                    """
                }
            }
        }
        // stage('Deploy'){
        //     // input {
        //     //     message "Should we continue?"
        //     //     ok "Yes, we should."
        //     //     submitter "alice,bob"
        //     //     parameters {
        //     //         string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
        //     //      }
        //     when { 
        //         expression { "$params.DEPLOY" == "true" }
        //     }
        //     steps {
        //          script{
        //             sh """
        //                 echo "Building"
        //             """
        //         }
        //     }
        // }
   }
   post {
        always{
            echo 'I will always say hello again'
            cleanWs()
        }
        success {
            echo 'I will always say hello again'
        }
        failure {
            echo 'I will always say hello again'
        }
        aborted {
            echo 'pipeline is timedout'
        }
   }
}