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
                  stage('Create Container Image') {
                    steps {
                         echo 'Create Container Image..'
        
                        script {

                          openshift.withCluster() { 
                               openshift.withProject("basic-java") {
  
                             def buildConfigExists = openshift.selector("bc", "image").exists() 
    
                          if(!buildConfigExists){ 
                          openshift.newBuild("--name=image", "--docker-image=registry.redhat.io/redhat-openjdk-18/openjdk18-openshift", "--binary") 
                             } 
    
                       openshift.selector("bc", "image").startBuild("--from-file=target/jb-hello-world-maven-0.2.0-shaded.war", "--follow") } }

                             }
                      }
                  }
         }
}
