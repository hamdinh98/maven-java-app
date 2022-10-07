pipeline {
    agent any
    tools{
    maven "maven-3.8.6"
    }
    stages {

        stage("test the app")
        {
          when{
             expression{
             branch "feature/Auth"
             }
          }
                 steps{
            echo "testing the app ..."
            echo "${BRANCH_NAME}"

          }
          
        }
        stage('build-jar') {

         when{
                     expression{
                     BRANCH_NAME = "master"
                     }
                  }
            steps {
                echo 'building maven project'
                sh "mvn package"
            }
        }
        stage("build docker image")
        {
         when{
                     expression{
                     BRANCH_NAME = "master"
                     }
                  }
            steps{
               echo "building docker images"
               sh "docker build -t hamdinh98/my-repo:java-1.0 ."
            }
        }
        stage("pushing docker image to dockerhub")
        {

         when{
                     expression{
                     BRANCH_NAME = "master"
                     }
                  }
         steps{
         echo "pushing docker images ... "
            withCredentials([usernamePassword(credentialsId: 'docker-hub-login', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
               echo "login to dockerhub images repos"
               sh "echo $PASSWORD | docker login -u $USERNAME --password-stdin"
               echo "push the images to dockerhub"
               sh "docker push hamdinh98/my-repo:java-1.0"
              }    
        }
                 
        }
    }
}
