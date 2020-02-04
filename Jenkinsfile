def FAILED_STAGE
pipeline{
    environment {
    registry = "chakri1998/fulldemo"
    registryCredential = 'dockerhub'
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
          script {
                    FAILED_STAGE=env.STAGE_NAME
                    echo "Cloning Git"
                }
        git branch:'master', url:'https://github.com/chakri1998/fulldemo.git'
        
      }
      
    }
         stage('Clean Old Packages') {
             
             steps{
                 script {
                    FAILED_STAGE=env.STAGE_NAME
                    echo "Clean Old Packages"
                }
                           dir("company/")
{
        sh label: 'Clean', script: 'mvn clean'
        
    }
             }
         }
        stage('Maven Compile') {
        steps{
            script {
                    FAILED_STAGE=env.STAGE_NAME
                    echo "Maven Compile"
                }
         dir("company/")
{
        sh label: 'Compile', script: 'mvn compile'
        
    }
        }
        }
        stage('Sonar Analysis'){
        steps{
            script {
                    FAILED_STAGE=env.STAGE_NAME
                    echo "Sonar Analysis"
                }
                dir("company/")
{
        withSonarQubeEnv('SonarQube'){
         sh 'mvn sonar:sonar'
         
            }
}
        }
        }

    stage('Maven install') {
    steps{
        script {
                    FAILED_STAGE=env.STAGE_NAME
                    echo "Maven Package"
                }
      dir("company/")
{
    sh label: 'Testing', script: 'mvn install'
    
    }
    }
    }
    stage('Jfrog Artifacory Upload')
    {
        steps{
    script{
        FAILED_STAGE=env.STAGE_NAME
        echo "Jfrog Artifacory Upload"
        def server= Artifactory.server 'Artifactory'
                    def uploadSpec= """{
                        "files": [{
                        "pattern": "company/target/*.war",
                        "target": "company"}]
                    }"""
        server.upload(uploadSpec)
        
        }
    }
    }
     
    stage('Jfrog Artifactory download'){
     
     steps{
     script{
         FAILED_STAGE=env.STAGE_NAME
         echo "Jfrog Artifacory Download"
    def server= Artifactory.server 'Artifactory'
    def downloadSpec = """{
    "files": [
    {
      "pattern": "company/company-0.0.1-SNAPSHOT.war",
      "target": "/home/mtadminnuvepro/artifacts"
      
    }
    ]
    }"""
    server.download(downloadSpec)
   
}
         
     }
  
    }

	stage('buldng image') {
      steps{
        dir("company/")
          {
        script {
            FAILED_STAGE=env.STAGE_NAME
          echo "create image"
          docker.build registry + ":$BUILD_NUMBER"
          
        }
      }
      }
    }
    stage('Assinging image to variable') {
      steps{
   dir("company/")
   {
        script {
            FAILED_STAGE=env.STAGE_NAME
           echo "Building image"
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
          
        }
      }
      }
    }
    stage('push Image to docker hub') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
              FAILED_STAGE=env.STAGE_NAME
            echo "Deploy Image"
            dockerImage.push()
            
          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
          script {
                    FAILED_STAGE=env.STAGE_NAME
                    echo "Remove Unused docker image"
                }
        sh "docker rmi $registry:$BUILD_NUMBER"
        
      }
    }
    stage('pull image from dockerhub'){
    steps{
        script{
             FAILED_STAGE=env.STAGE_NAME
        echo "pull image from dockerhub"
             sh "docker pull $registry:$BUILD_NUMBER"
            
    }
    }
  }
  stage('deployee the image in the tomcat server'){
      steps{
          script{
              FAILED_STAGE=env.STAGE_NAME
               echo "deployee the image in the tomcat server"
              sh"docker run -d -it -p 8085:8080 --name companyproject $registry:$BUILD_NUMBER"
              
          }
      }
  }
  stage('email'){
      steps{
          mail bcc: '', body: 'your pipeline is success enjoy man party.............', cc: '', from: '', replyTo: '', subject: 'build success', to: 'Devarakonda.Chakradhar@mindtree.com'
      }
  }
  }

	 post 
    {  
        always 
        {  
            echo 'This will always run'  
        }
        success {
            mail bcc: '', body: 'project is successfully builded', from: '', replyTo: '', subject: 'project successfully finished.', to: 'Devarakonda.Chakradhar@mindtree.com'
        }
        failure {
            mail bcc: '', body:"Failed stage name: ${FAILED_STAGE}; This needs to be resolved... ${env.BUILD_URL} has result ${currentBuild.result}", from: '', replyTo: '', subject:"Status of pipeline: ${currentBuild.fullDisplayName}", to: 'Devarakonda.Chakradhar@mindtree.com'
        }
    }  

}
