pipeline {
 agent { node { label "maven-sonar" } }
 environment {
        PATH = "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$PATH"
    }
 parameters   {
   string(name: 'aws_account', defaultValue: '339712774185', description: 'aws account hosting image registry')
   string(name: 'ecr_tag', defaultValue: '1.0.0' , description: 'Choose the ecr tag version for the build')
       }
tools {    maven "maven3.9.8"
    }
    stages {
      stage('1. Git Checkout') {
        steps {
          git branch: 'main', credentialsId: 'git-credential', url: 'https://github.com/bhisawlah/addressbook.git'
        }
      }
      stage('2. Build with maven') { 
        steps{
          sh "mvn clean package"
         }
       }
      stage('3. SonarQube analysis') {
      environment {SONAR_TOKEN = credentials('sonarqube')
      }
      steps {
       script {
         def scannerHome = tool 'SonarQube-Scanner 6.0.0';
         withSonarQubeEnv("sonarqube-integration") {
         sh "${tool("SonarQube-Scanner 6.0.0")}/bin/sonar-scanner -X \
           -Dsonar.projectKey=addressbook-application2 \
           -Dsonar.projectName='addressbook-application2' \
           -Dsonar.host.url=http://52.3.220.181:9000 \
           -Dsonar.token=$SONAR_TOKEN \
           -Dsonar.sources=src/main/java/ \
           -Dsonar.java.binaries=target/classes"
          }
         }
       }
      }
      stage('4. Docker image build') {
      steps {
        script {
            // Login to AWS ECR
            def ecrLoginCmd = "aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin ${params.aws_account}.dkr.ecr.us-west-2.amazonaws.com"
            sh ecrLoginCmd
            
            // Build Docker image
            sh "sudo docker build -t addressbook ."
            
            // Tag Docker image
            sh "docker tag addressbook:latest ${params.aws_account}.dkr.ecr.us-west-2.amazonaws.com/addressbook:${params.ecr_tag}"
            
            // Push Docker image to ECR
            sh "docker push ${params.aws_account}.dkr.ecr.us-west-2.amazonaws.com/addressbook:${params.ecr_tag}"
        }
    }
}
      stage('5. Application deployment in eks') {
        steps{
          kubeconfig(caCertificate: '',credentialsId: 'k8s-kubeconfig', serverUrl: '') {
          sh "kubectl apply -f manifest"
          }
         }
       }
      stage('6. Monitoring solution deployment in eks') {
        steps{
          kubeconfig(caCertificate: '',credentialsId: 'k8s-kubeconfig', serverUrl: '') {
          sh "kubectl apply -f monitoring"
          sh "chmod +x -R script"
          sh(""" script/createIRSA-AMPIngest.sh""")
          sh(""" script/createIRSA-AMPQuery.sh""")
          }
         }
       }
      stage ('7. Email Notification') {
         steps{
         mail bcc: 'ajisegbedeabisolat@gmail.com', body: '''Build is Over. Check the application using the URL below. 
         https//abook.shiawslab.com/addressbook-1.0
         Let me know if the changes look okay.
         Thanks,
         Dominion System Technologies,
         +1 (313) 413-1477''', cc: 'ajisegbedeabisolat@gmail.com', from: '', replyTo: '', subject: 'Application was Successfully Deployed!!', to: 'ajisegbedeabisolat@gmail.com'
      }
    }
 }
}
