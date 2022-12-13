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
            stage('create container image')
            {
                steps
                {
                    echo 'create vconatiner image'
                 script
                    {
                        openshift.withkCluster()
                        {
                            openshift.withProject("basic-java")
                            {
                                def buildConfigExists = openshift.selector("bc","image").exists()
                                if(!buildConfigExists)
                                {
                                    openshift.newBuild("--name=image"."--docker-image=redhat.registry.io/redhat-openjdk-18/openjdk18-openshift","--binary")
                                }
                                openshift.selector("bc","image").startbuild("--from-file=target/jb-hello-world-maven-0.2.0.jar","--follow")
                            }
                        }
                    }
                }
            }
         }
}
