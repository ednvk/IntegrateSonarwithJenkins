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
3. Start and check Jenkins Status
   ```
    sudo systemctl start jenkins
    sudo systemctl enable jenkins
    sudo systemctl status jenkins
   ```
3. use the ur IP address to browse the Jenkins server and port 8080, like below  

   http://your-ec2-public-IP:8080

4. Retrieve the initial admin password using the below command:  
    ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```
5. Copy the password into the Jenkins setup page.
6. Install suggested plugins.
7. Create your first Admin User.
   
## Now set up the SonarQube in the Jenkins

### Before you set up, install Sonarqube in the Sonarqube server- Instance type t2.medium. it is mandatory.

```
sudo su -

adduser sonarqube >> give passwords and user info

apt install unzip -y

apt install openjdk-21-jre -y

sudo su - sonarqube

wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-25.8.0.112029.zip

unzip sonarqube-25.8.0.112029.zip

rm -rf sonarqube-25.8.0.112029.zip

chmod -R 755 /home/sonarqube/sonarqube-25.8.0.112029/
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-25.8.0.112029/
ll
cd sonarqube-25.8.0.112029/bin/linux-x86-64/

./sonar.sh start

./sonar.sh status
```
   

