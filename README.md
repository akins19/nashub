node {

stage ('Checkout')  {
tool name: 'Maven3', type: 'maven'
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
