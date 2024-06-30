pipeline {
<<<<<<< HEAD
 agent { node { label "maven-sonar" } }
 parameters   {
   string(name: 'aws_account', defaultValue: '322266404742', description: 'aws account hosting image registry')
   string(name: 'ecr_tag', defaultValue: '1.0.0' , description: 'Choose the ecr tag version for the build')
       }
tools {    maven "maven3.9.8"
=======
 agent { node { label "maven-sonarqube-node" } }
 parameters   {
   choice(name: 'aws_account',choices: ['322266404742', '4568366404742', '922266408974'], description: 'aws account hosting image registry')
   choice(name: 'ecr_tag',choices: ['1.0.0','1.1.0','1.2.0'], description: 'Choose the ecr tag version for the build')
       }
tools {
    maven "Maven-3.9.8"
>>>>>>> 2ce876e1e4a99609bc64b36e849970c9a4613268
    }
    stages {
      stage('1. Git Checkout') {
        steps {
<<<<<<< HEAD
          git branch: 'main', credentialsId: 'git-credential', url: 'https://github.com/bhisawlah/addressbook.git'
=======
          git branch: 'main', credentialsId: 'Github-pat', url: 'https://github.com/ndiforfusi/addressbook.git'
>>>>>>> 2ce876e1e4a99609bc64b36e849970c9a4613268
        }
      }
      stage('2. Build with maven') { 
        steps{
          sh "mvn clean package"
         }
       }
      stage('3. SonarQube analysis') {
<<<<<<< HEAD
      environment {SONAR_TOKEN = credentials('sonarqube')
      }
      steps {
       script {
         def scannerHome = tool 'SonarQube-Scanner 6.0.0';
         withSonarQubeEnv("sonarqube-integration") {
         sh "${tool("SonarQube-Scanner 6.0.0")}/bin/sonar-scanner -X \
           -Dsonar.projectName='addressbook-application' \
           -Dsonar.projectKey=addressbook-application \
           -Dsonar.host.url=http://34.227.187.57:9000 \
=======
      environment {SONAR_TOKEN = credentials('sonar-token-abook')}
      steps {
       script {
         def scannerHome = tool 'SonarQube-Scanner-6.0.0';
         withSonarQubeEnv("sonarqube-integration") {
         sh "${tool("SonarQube-Scanner-6.0.0")}/bin/sonar-scanner  \
           -Dsonar.projectKey=addressbook-application \
           -Dsonar.projectName='addressbook-application' \
           -Dsonar.host.url=https://sonar.shiawslab.com \
>>>>>>> 2ce876e1e4a99609bc64b36e849970c9a4613268
           -Dsonar.token=$SONAR_TOKEN \
           -Dsonar.sources=src/main/java/ \
           -Dsonar.java.binaries=target/classes"
          }
         }
       }
      }
      stage('4. Docker image build') {
         steps{
<<<<<<< HEAD
          sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${params.aws_account}.dkr.ecr.us-east-1.amazonaws.com"
          sh "sudo docker build -t addressbook ."
          sh "docker tag addressbook:latest ${params.aws_account}.dkr.ecr.us-east-1.amazonaws.com/addressbook:${params.ecr_tag}"
          sh "docker push ${params.aws_account}.dkr.ecr.us-east-1.amazonaws.com/addressbook:${params.ecr_tag}"
=======
          sh "aws ecr get-login-password --region us-west-2 | sudo docker login --username AWS --password-stdin ${params.aws_account}.dkr.ecr.us-west-2.amazonaws.com"
          sh "sudo docker build -t addressbook ."
          sh "sudo docker tag addressbook:latest ${params.aws_account}.dkr.ecr.us-west-2.amazonaws.com/addressbook:${params.ecr_tag}"
          sh "sudo docker push ${params.aws_account}.dkr.ecr.us-west-2.amazonaws.com/addressbook:${params.ecr_tag}"
>>>>>>> 2ce876e1e4a99609bc64b36e849970c9a4613268
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
         mail bcc: 'fusisoft@gmail.com', body: '''Build is Over. Check the application using the URL below. 
         https//abook.shiawslab.com/addressbook-1.0
         Let me know if the changes look okay.
         Thanks,
         Dominion System Technologies,
         +1 (313) 413-1477''', cc: 'fusisoft@gmail.com', from: '', replyTo: '', subject: 'Application was Successfully Deployed!!', to: 'fusisoft@gmail.com'
      }
    }
 }
<<<<<<< HEAD
}
=======
}



>>>>>>> 2ce876e1e4a99609bc64b36e849970c9a4613268
