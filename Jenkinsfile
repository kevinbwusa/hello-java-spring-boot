// Based on:
// https://raw.githubusercontent.com/redhat-cop/container-pipelines/master/basic-spring-boot/Jenkinsfile

library identifier: "pipeline-library@v1.5",
retriever: modernSCM(
  [
    $class: "GitSCMSource",
    remote: "https://github.com/redhat-cop/pipeline-library.git"
  ]
)

// URL and REf to the Spring Boot application
appSourceUrl = "https://github.com/kevinbwusa/hello-java-spring-boot.git"
appSourceRef = "main"

// Folder containing the Help pipeline
appFolder = "app"

// The name you want to give your Spring Boot application
// Each resource related to your app will be given this name
appName = "hello-java-spring-boot"

pipeline {
    // Use the 'maven' Jenkins agent image which is provided with OpenShift 
    agent { label "maven" }
    stages {
        stage("Checkout") {
            steps {
                // This creates a separate folder to clone the Spring Boot app to
                sh "mkdir ${appFolder}"
                dir(appFolder) {
                    git url: "${appSourceUrl}", branch: "${appSourceRef}"
                }
                // checkout scm
            }
        }
        stage("Get Version from POM") {
            steps {
                script {
                    dir(appFolder) {
                        tag = readMavenPom().getVersion()
                    }
                }
            }
        }
        stage("Docker Build") {
            steps {
                dir(appFolder) {
                    // This uploads your application's source code and performs a binary build in OpenShift
                    // This is a step defined in the shared library (see the top for the URL)
                    // (Or you could invoke this step using 'oc' commands!)
                    binaryBuild(buildConfigName: appName, buildFromPath: ".")
                }
            }
        }

        // You could extend the pipeline by tagging the image,
        // or deploying it to a production environment, etc......
    }
}
