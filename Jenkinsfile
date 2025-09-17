pipeline {
    agent any
    
    triggers {
        githubPush()
    }

    environment {
        // Nexus details (configure as per your setup)
        NEXUS_URL = 'http://ec2-18-208-191-13.compute-1.amazonaws.com:8081/repository/maven-releases/'
        NEXUS_CREDENTIALS = 'nexus-cred-id'  // Jenkins credential ID for Nexus username/password
        GROUP_ID = 'com.example'
        ARTIFACT_ID = 'snappy-app'
        VERSION = '1.0.${BUILD_NUMBER}'
        PACKAGING = 'war'
    }

    stages {

        stage('CLONE SCM') {
            steps {
                echo 'Cloning source code from GitHub...'
                git branch: 'main', url: 'https://github.com/supriyan3/mindcircuit16d.git'
            }
        }

        stage('Build Artifact') {
            steps {
                echo 'Building code using Maven...'
                sh 'mvn clean package'
            }
        }

        stage('Upload to Nexus') {
            steps {
                echo 'Uploading artifact to Nexus...'
                withCredentials([usernamePassword(credentialsId: "${NEXUS_CREDENTIALS}", 
                                                 usernameVariable: 'NEXUS_USER', 
                                                 passwordVariable: 'NEXUS_PASS')]) {
                    sh """
                        mvn deploy:deploy-file \
                          -DgroupId=${GROUP_ID} \
                          -DartifactId=${ARTIFACT_ID} \
                          -Dversion=${VERSION} \
                          -Dpackaging=${PACKAGING} \
                          -Dfile=target/${ARTIFACT_ID}.war \
                          -DrepositoryId=nexus \
                          -Durl=${NEXUS_URL} \
                          -Dusername=$NEXUS_USER \
                          -Dpassword=$NEXUS_PASS
                    """
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo 'Deploying .war from Nexus to Tomcat...'
                deploy adapters: [
                    tomcat9(
                        alternativeDeploymentContext: '', 
                        credentialsId: 'tomcat', 
                        path: '', 
                        url: 'http://ec2-3-82-24-148.compute-1.amazonaws.com:8081/'
                    )
                ], contextPath: 'Snappy-APP', war: '**/*.war'
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}





