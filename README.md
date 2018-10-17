node {

stage ('Checkout')  {
checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'd8801080-9657-499d-a3de-d5579dd4a898', url: 'https://bitbucket.org/nakins/step/src/master/']]])
                    }

stage ('Build')
        {
            def mvnHome = tool 'Maven3'
           
       withSonarQubeEnv('Demo') { 
       sh "${mvnHome}/bin/mvn clean install -f MyWebApp/pom.xml sonar:sonar"
        }

        }

stage ('DEV Deploy')
         {
                echo "deploying to DEV tomcat "
               sh 'sudo cp /var/lib/jenkins/workspace/demo1/MyWebApp/target/MyWebApp.war /var/lib/tomcat8/webapps'
      }

stage ('DEV Approve')
      {
            echo "Taking approval from DEV"
                
            timeout(time: 7, unit: 'DAYS') {
            input message: 'Do you want to deploy?', submitter: 'admin'
            } 
     }
}
