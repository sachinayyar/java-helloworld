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
  
                             def buildConfigExists = openshift.selector("bc", "java-helloworld").exists() 
    
                          if(!buildConfigExists){ 
                          openshift.newBuild("--name=java-helloworld", "--docker-image=registry.redhat.io/jboss-eap-7/eap74-openjdk8-openshift-rhel7", "--binary") 
                             } 
    
                       openshift.selector("bc", "java-helloworld").startBuild("--from-file=target/jb-hello-world-maven-0.2.0-SNAPSHOT.jar", "--follow") 
                                   
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
              def deployment = openshift.selector("dc", "java-helloworld") 
     
              if(!deployment.exists()){ 
                 openshift.newApp('java-helloworld', "--as-deployment-config").narrow('svc').expose() 
                  } 
    
                      timeout(5) { 
                           openshift.selector("dc", "java-helloworld").related('pods').untilEach(1) { 
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



