# IntegrateSonarwithJenkins
## Install Jenkins in AWS EC2

**Note:** Use instance type - t2.medium

Once the Instance is created, follow the steps below:

1. First update the system.

    ```bash
    sudo apt update
    ```

2. Install Java version 21 and Jenkins

    ```bash
    sudo apt install openjdk-21-jdk
  
    sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
      https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] \
      https://pkg.jenkins.io/debian-stable binary/" | sudo tee \
      /etc/apt/sources.list.d/jenkins.list > /dev/null
    
    sudo apt-get update
    
    sudo apt-get install jenkins
    ```

3. use the ur IP address to browse the Jenkins server and port 8080 like below
   http://<your-ec2-public-IP>:8080
   

