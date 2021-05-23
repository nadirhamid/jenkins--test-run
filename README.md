# Jenkins pipeline

# Requirements

```
Ubuntu 18.04 (bionic distro preferred)
Jenkins
Docker
```

# Installation

1. SSH into Ubuntu box
2. Install Jenkins 
3. Install Docker
4. Add Jenkins to docker group
```
sudo usermod -aG docker jenkins
```
5. Update access to Docker's socket file
```
chmod 777 /var/run/docker.sock
```
# Setup Jenkins 

You can open a browser and view your install at http://<your-host>:8080/.

Please install your Jenkins server. You can use all the default settings.
## Install Docker plugins

In order for Jenkins to be able to use Docker commands, we need to install Docker plugins. To install Docker plugins:

Go to Manage Jenkins → Manage Plugins.

Search Docker Pipelines, click on Install without restart.

## Setup Credentials

We will need to setup credentials so Jenkins can pull images from our Docker repoistory. You can setup credentials using the Jenkins credentials manager. To setup your credentials, please use the following steps

1. In Jenkins dashboard go to Manage Jenkins → Manage Credentials
2. Under stores click Jenkins
3. Go to Global credentials
4. In the top left click Add credentials
5. Please add a credentials with the following settings:
```
scope: Global
username: <your-dockerhub-username>
password: <your-dockerhub-password>
ID: dockerhub_id
```
6. Save


## Create Pipeline

Our pipeline will be a basic setup that builds a Docker image and pushes it to a Docker repository. 

Please use steps below to setup the pipeline:

1. In Jenkins Dashboard please click New item
2. Enter item name. For example: my-jenkins-project
3. Select Pipeline
4. Click OK

## Configure pipeline

We will need to configure pipeline so we can pull our Github code and build our Docker image.

In the Pipeline definition please enter the following script:

```
pipeline {

    environment {

        registry = "matrixnad/jenkins-docker"

        registryCredential = 'dockerhub_id'

        dockerImage = 'jenkins-docker'

    }

    agent any

    stages {

        stage('Cloning our Git') {

            steps {

                git 'https://github.com/nadirhamid/jenkins-test-run.git'

            }

        }

        stage('Building our image') {

            steps {

                script {

                    dockerImage = docker.build registry + ":$BUILD_NUMBER"

                }

            }

        }

        stage('Deploy our image') {

            steps {

                script {

                    docker.withRegistry( '', registryCredential ) { 

                        dockerImage.push()
                    }


                }

            }

        }

        stage('Cleaning up') {

            steps {

                sh "docker rmi $registry:$BUILD_NUMBER"

            }

        }

    }

}
```

Then click save

## Testing pipeline

To test your pipeline, please go to the project main page and click 'Build Now'

## Monitor build status

You can view build success/failures in the console section. All successful results will be shown with a green light, and any failed attempts will appear in red.

# Conclusion

This basic readme went over a simple Docker-Jenkins deployment. I used my personal Github project to demonstrate the build and provided all instructions to setup the solution on your end. 


# Contact

If you have any questions / need more info please reach out to me at:

```
Nadir Hamid <matrix.nad@gmail.com>
```

Thanks for reading





