pipeline {
    agent any
    triggers {
        pollSCM('*/1 * * * *')
    }

    stages {
        stage('build-docker-image') {
            steps{
                build_docker_image()
            }
        }
        stage('deploy-dev') {
            steps {
                deploy("dev")
            }
        }
        stage('tests-on-dev') {
            steps {
                run_tests("DEV")
            }
        }
        stage('deploy-to-stg') {
            steps {
                deploy("stg")
            }
        }
        stage('tests-on-stg') {
            steps {
                run_tests("STG")
            }
        }
        stage('deploy-to-prd') {
            steps {
                deploy("prd")
            }
        }
        stage('tests-on-prd') {
            steps {
                run_tests("PRD")
            }
        }
    }
}


def build_docker_image() {
    echo "Building docker image.. "
    git branch: 'main', url: 'https://github.com/StepansSotskovsTestdevlabCom/python-greetings.git'

    sh "docker build --no-cache -t stepanssotskovs/python-greetings-app:latest ."

    echo "Pushing docker image to the docker registry"
    sh "docker push stepanssotskovs/spython-greetings-app:latest"   
}


def deploy(String environment){
    echo "Deployment triggered to ${environment} environemnt.. "
    sh "docker pull stepanssotskovs/python-greetings-app:latest"

    sh "docker compose stop greetings-app-${environment}"
    sh "docker compose rm greetings-app-${environment}"
    sh "docker compose up -d greetings-app-${environment}"
}

def run_tests(String environment){
   echo "API Tests triggered against ${environment} environemnt.. "
   sh "docker pull stepanssotskovs/api-tests:latest"
   sh "docker run --network=host --rm stepanssotskovs/api-tests run greetings greetings_${environment}"
}