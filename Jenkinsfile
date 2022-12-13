pipeline 
{
  agent
  {
      label 'maven'
  }
         stages
        {
            stage('BUILD')
            {
                steps
                {
                    echo 'building'
                    sh 'mvn clean package'
                }
            }
            
            
            
                  stage('Create Container Image') 
                  {
                    steps 
                    {
                         echo 'Create Container Image..'
        
                        script 
                        {

                          openshift.withCluster() { 
                               openshift.withProject("basic-java") {
  
                             def buildConfigExists = openshift.selector("bc", "image").exists() 
    
                          if(!buildConfigExists){ 
                          openshift.newBuild("--name=image", "--docker-image=registry.redhat.io/redhat-openjdk-18/openjdk18-openshift", "--binary") 
                             } 
    
                       openshift.selector("bc", "image").startBuild("--from-file=target/jb-hello-world-maven-0.2.0-SNAPSHOT.jar", "--follow") 
                                   
                               } }

                             }
                      }
                  }
        
          stage('Deploy') {
      steps {
        echo 'Deploying....'
        script {

          openshift.withCluster() { 
            openshift.withProject("basic-java") { 
              def deployment = openshift.selector("dc", "codelikethewind") 
     
              if(!deployment.exists()){ 
                 openshift.newApp('codelikethewind', "--as-deployment-config").narrow('svc').expose() 
                  } 
    
                      timeout(5) { 
                           openshift.selector("dc", "codelikethewind").related('pods').untilEach(1) { 
                           return (it.object().status.phase == "Running") 
                                 } 
                            } 
                         } 
                      }
        }
         }
                }
}

}



