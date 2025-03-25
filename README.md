# Docker-on-Jenkins
Deploy Docker on Jenkins Slave and run the docker image on the Slave Node we must have the following configurations on our instance
1. EC2 instance with incoming traffic enabled on required ports
2. Java must be intsalled
3. Jenkins has to be installed which will work only with java
4. Install Docker

**Run this Command to install apt packages and java on the EC2 instance**

```
sudo apt update -y
sudo apt install fontconfig openjdk-17-jre
java -version
openjdk version "17.0.13" 2024-10-15
```


**Run this command to install Jenkins on EC2 instance. Jenkins default port is 8080**

```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

**To enable Jenkins on EC2**
```
sudo systemctl enable jenkins
```

**To start Jenkins services on EC2**
```
sudo systemctl start jenkins
```

**To check the status of Jenkins Installed on EC2**
```
sudo systemctl status jenkins
````

**To retrieve the password of Jenkins Admin**
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

**To retrieve the port of Jenkins**
```
sudo cat /etc/default/jenkins | grep HTTP_PORT
```

**To install docker on EC2**
```
sudo apt install docker.io
```

**Give Permissions of Jenkins and Ubuntu user for Docker Daemon**
```
sudo usermod -aG docker jenkins
sudo usermod -aG docker ubuntu
sudo systemctl restart docker
```

**Switch to jenkins user and run docker containers**
```
sudo - jenkins
docker run hello-world
```

**Now Connect to Jenkins server on browser using**

```
http://<public-Ip of EC2>:Jenkins Port
```


Install reuired Plugins like Docker on Jenkins Master.
Now Configure Slave Node using following the steps
1. Node Name and declare as permanent
2. Slave Node Remote root directory: /home/jenkins/agent

**We have to run Jenkins slave on Terminal **

```
mkdir -p /home/jenkins/agent
cd /home/jenkins/agent
```

**Install JAR Files on the server
**
```
wget http://<JENKINS_MASTER_IP>:8080/jnlpJars/agent.jar
```

Now to connect to Slave Node in Status Curl Command will be seen run it on the terminal

Now Create a Pipeline Project and describe the pipeline using Pipeline Script and Save it

```
pipeline {
    agent { label 'docker-agent' }  // Jenkins Slave lo run cheyyadam ki

    stages {
        stage('Pull Docker Image') {
            steps {
                script {
                    sh 'docker pull kowshikrampalli/my-python-app:latest'
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    sh 'docker run -d --name my-python-container -p 5000:5000 kowshikrampalli/my-python-app:latest'
                }
            }
        }
    }
}
```

Use the Build Now to run the container on Slave Node. 

To verify it is running use listing command to check the successfully elapsed containers.

```
docker ps -a
```


