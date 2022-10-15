#!usr/bin/env groovy
@Library('jenkins-shared-library')_
pipeline {
    agent any
    tools{
    maven "maven"
    }
    stages {
        stage("test maven"){
            steps{
                sh "mvn -version" 
            }
        }
        stage("Increment version")
        {
            steps{
                script{
                sh 'mvn build-helper:parse-version \
                    -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                    versions:commit'
                def matcher =  readFile('pom.xml')=~'<version>(.+)</version>'
                def version = matcher[0][1]
                env.IMAGE_NAME= "$version-$BUILD-NUMBER"
            }
                
            }
        }
        stage("stage for feature/Auth")
        {
          when{
             expression{
             branch "feature/Auth"
             }
          }
                 steps{
            echo "testing the app ..."
          }
          
        }
        stage('build-jar') {
         when{
               expression{
                     branch "master"
                     }
                  }
            steps {
                echo 'building maven project'
                buildJar()
            }
        }
        
        stage('Unit test')
        {
            steps{
                echo " testing the app .."
                sh "mvn test"
            }
        }
        stage("build docker image")
        {
         when{
                     expression{
                     branch "master"
                     }
                  }
            steps{
               echo "building docker images"
                buildImage("hamdinh98/my-repo:java-${IMAGE_NAME}")
            }
        }
        stage("pushing docker image to dockerhub")
        {

         when{
                     expression{
                     branch "master"
                     }
                  }
         steps{
         echo "pushing docker images ... "
            withCredentials([usernamePassword(credentialsId: 'docker-hub-login', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
               echo "login to dockerhub images repos"
               sh "echo $PASSWORD | docker login -u $USERNAME --password-stdin"
               echo "push the images to dockerhub"
               sh "docker push hamdinh98/my-repo:java-${IMAGE_NAME}"
              }    
        }
                 
        }
    }
}
