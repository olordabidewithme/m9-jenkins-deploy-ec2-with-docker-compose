Project Title:
CD - Deploy Application from Jenkins Pipeline on EC2 Instance (Automatically with Docker Compose)

Technologies Used:
	AWS, Jenkins, Docker, Linux, Git, Java, Maven, Docker Hub
Project Description:
	Install Docker Compose on AWS EC2 instance.
	Create docker-compose.yml file that deploys our web application image.
	Configure Jenkins pipeline to deploy newly built image using Docker Compose on EC2 server.
	Improvement: Extract multiple Linux commands that are executed on remote server into a separate shell script and execute the script from Jenkinsfile.

- Install docker-compose on EC2 instance
	- sudo curl -L https://github.com/docker/copose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
	- sudo chmod +x /usr/local/bin/docker-compose
	- docker-compose --version

- Create docker-compose.yaml file
	- create docker-compose.yaml file
	- java maven docker image
	- third-party postgres docker image
```bash
version: '3.8'
services:
	java-maven-app:
		image: olordabidewithme/demo-app:java-maven-1.0
		ports:
			- 8080:8080
	postgres:
		image: postgres:15
		ports:
			- 5432:5432
		environment:
			-POSTGRES_PASSWORD=my-pwd
```
- Put docker-compose.yaml file on EC2 instance
- Adjust Jenkinsfile to execute docker-compose command on EC2 instance
```bash
stage("deploy") {
	steps {
		script {
			echo 'deploying docker image to EC2...'
			def dockerComposeCmd = "docker-compose -f docker-compose.yaml up --detach"
			sshagent(['ec2-server-key']) {
				sh "scp docker-compose.yal ec2-user@ec2-ip-address:/home/ec2-user"
				sh "ssh -o StrictHostKeyChecking=no ec2-user@ec2-ip-address ${dockerComposeCmd}"
			}
		}
	}
}

```	

- Push the changes, CD trigger
- Check if container of java application and postgres is started and running
	- docker ps



--Additional Notes :
The command you provided does the following:

bash

sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
Breakdown of the Command:
sudo: Runs the command with superuser (administrator) privileges, which is often necessary for installing software in system directories.

curl -L:

curl is a command-line tool for transferring data with URLs.
The -L option tells curl to follow any redirects that may occur when accessing the URL.
https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m):

This is the URL from which the Docker Compose binary is being downloaded.
$(uname -s) gets the operating system name (e.g., Linux).
$(uname -m) gets the machine hardware name (e.g., x86_64).
This constructs a URL to download the latest version of Docker Compose for the specific OS and architecture.
-o /usr/local/bin/docker-compose:

The -o option specifies the output file where the downloaded file will be saved.
In this case, it saves the Docker Compose binary to /usr/local/bin/docker-compose, which is a common directory for executable files.
Summary:
This command downloads the latest Docker Compose binary for your operating system and architecture and saves it to /usr/local/bin/docker-compose, making it available for execution from the command line.
