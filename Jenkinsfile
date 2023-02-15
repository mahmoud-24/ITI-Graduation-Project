pipeline {
    agent any

    stages {

        stage('CI'){
            steps {

                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')])            {

                sh """
                    docker build . -t mmelegy/python-app:v$BUILD_NUMBER
                    docker login -u ${USERNAME} -p ${PASSWORD}
                    docker push mmelegy/python-app:v$BUILD_NUMBER
                """
                }
              }
        }

        stage('CD'){
            steps {

                withCredentials([file(credentialsId: 'srv-access', variable: 'config')]){
                    sh """
                        gcloud auth activate-service-account --key-file=${config}
                        gcloud container clusters get-credentials private-cluster --zone us-east4-b --project mahmoud-ibrahim-2023
                        sed -i 's/tag/${BUILD_NUMBER}/g' deployment/deployment.yaml
                        kubectl apply -Rf deployment
                    """
                }
            }
 
        }
    }
}