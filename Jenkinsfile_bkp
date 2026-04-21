pipeline {
    agent {
        node {
            label 'AGENT-1'
        }
    }

    environment {
        COURSE = "Jenkins"
        appVersion = ""
        ACC_ID = "307223751858"
        PROJECT = "roboshop"
        COMPONENT = "catalogue"
        REGION = "us-east-1"
    }

    options {
        timeout(time: 30, unit: 'MINUTES') 
        disableConcurrentBuilds()
    }

    parameters {
        string(name: 'appVersion', description: 'Which app version you want to deploy')
        choice(name: 'deploy_to', choices: ['dev', 'qa', 'prod'], description: 'Pick something')
    }

    stages {

        stage('Deploy') {
            steps {
                script {
                    withAWS(region:'us-east-1', credentials:'aws-creds') {
                        sh """
                            aws eks update-kubeconfig --region ${REGION} --name ${PROJECT}-${params.deploy_to}
                            kubectl get nodes
                        """
                    }
                }
            }
        }

        stage('Trigger DEV Deploy') {
            steps {
                script {
                    build(
                        job: "catalogue-deploy",   // change only if your job name is different
                        wait: false,
                        propagate: false,
                        parameters: [
                            string(name: 'appVersion', value: "${params.appVersion}"),
                            string(name: 'deploy_to', value: "dev")
                        ]
                    )
                }
            }
        }

    }

    post {
        always {
            echo 'I will always say Hello again!'
            cleanWs()
        }
        success {
            echo 'I will run if success'
        }
        failure {
            echo 'I will run if failure'
        }
        aborted {
            echo 'pipeline is aborted'
        }
    }
}