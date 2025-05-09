pipeline {
    agent { node { label "node-slave" } }
    
    environment {
        PATH = "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$PATH"
        KUBECONFIG_CREDENTIALS_ID = 'k8s-kubeconfig'
        K8S_SERVER_URL = 'https://870A84ED50DD1BA68F75B592833E0827.gr7.us-east-2.eks.amazonaws.com'
    }
    
    parameters {
        string(name: 'aws_account', defaultValue: '339712774185', description: 'AWS account hosting image registry')
        string(name: 'ecr_tag', defaultValue: '1.0.0', description: 'Choose the ECR tag version for the build')
    }
    
    tools {
        maven "maven3.9.8"
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-credential', url: 'https://github.com/bhisawlah/addressbook.git'
            }
        }
        
        stage('Build with Maven') {
            steps {
                sh "mvn clean package"
            }
        }
        
        stage('SonarQube analysis') {
            environment {
                SONAR_TOKEN = credentials('sonarqube-cred')
            }
            steps {
                script {
                    withSonarQubeEnv("sonarqube-integration") {
                        sh "${tool('SonarQube-Scanner 6.0.0')}/bin/sonar-scanner -X \
                            -Dsonar.projectKey=elitebook-app \
                            -Dsonar.projectName='elitebook-app' \
                            -Dsonar.host.url=http://100.24.83.181:9000 \
                            -Dsonar.token=$SONAR_TOKEN \
                            -Dsonar.sources=src/main/java/ \
                            -Dsonar.java.binaries=target/classes"
                    }
                }
            }
        }
        
        stage('Docker image build') {
            steps {
                script {
                    // Login to AWS ECR
                    def ecrLoginCmd = "aws ecr get-login-password --region us-west-1 | sudo docker login --username AWS --password-stdin ${params.aws_account}.dkr.ecr.us-west-1.amazonaws.com"
                    sh ecrLoginCmd
                    
                    
                    // Build Docker image
                    sh "sudo docker build -t addressbook ."
                    
                    // Tag Docker image
                    sh "sudo docker tag addressbook:latest ${params.aws_account}.dkr.ecr.us-west-1.amazonaws.com/addressbook:${params.ecr_tag}"
                    
                    // Push Docker image to ECR
                    sh "sudo docker push ${params.aws_account}.dkr.ecr.us-west-1.amazonaws.com/addressbook:${params.ecr_tag}"
                }
            }
        }
        
        stage('Application deployment in eks') {
            steps {
                kubeconfig(caCertificate: '',credentialsId: 'k8s-kubeconfig', serverUrl: '') {
          sh "kubectl apply -f manifest"
          }
         }
       }
        /*
        stage('Monitoring solution deployment in eks') {
            steps {
                kubeconfig(caCertificate: '', credentialsId: 'k8s-kubeconfig', serverUrl: '') {
                    sh "kubectl apply -f monitoring"
                    sh "chmod +x -R script"
                    sh "script/createIRSA-AMPIngest.sh"
                    sh "script/createIRSA-AMPQuery.sh"
                }
            }
        }
        */
        
        stage('Email Notification') {
            steps {
                mail bcc: 'ajisegbedeabisolat@gmail.com', body: '''
                    Build is Over. Check the application using the URL below.
                    https//abook.shiawslab.com/addressbook-1.0
                    Let me know if the changes look okay.
                    Thanks,
                    Dominion System Technologies,
                    +1 (313) 413-1477''', cc: 'ajisegbedeabisolat@gmail.com', from: '', replyTo: '', subject: 'Application was Successfully Deployed!!', to: 'ajisegbedeabisolat@gmail.com'
            }
        }
    }
}
