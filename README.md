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
Now use ur Sonar server public IP to connect it from the web  

http.//your-public-ip:9000  

Set up the Sonarqube credentials

## Steps to Integrate SonarQube with Jenkins
1. In the Sonar server (S), generate a token in the SonarQube Server for the project you created locally and copy it.
2. Go to Jenkins Server (J), add the token in the Jenkins credentials section.
3. In J, go to manage Jenkins → install Sonar Scanner for Jenkins.  
4. In J, go to manage Jenkins → system → SonarQube servers → Sonarqube-v25 to use a URL and Secret text credential containing a valid Sonar user token that you copied previously from the S server.
5. In J, add the sonarScanner in the tools section. At the name params, use the same name(Sonarqube-v25) that you used in the Environment << SonarQube servers << System << Manage Jenkins.
6. Now create a project in J server >> pipeline and use the below Groovy code to trigger the SonarScanner, which will check the code quality and vulnerabilities in your code.
7. In S Server, go to Administration>> Configuration >> Webhook, provide the Jenkins URL trailing sonarqube-webhook
   http://your-jenkins-ip:8080/sonarqube-webhook/

```
pipeline {
  agent any

  environment { PATH = "$PATH:/usr/share/maven/bin" }

  stages {
    stage('GetCode') {
      steps {
        deleteDir()
        git branch: 'main', url: 'https://github.com/ednvk/JavaWebCalculator.git'
      }
    }

    stage('Build') {
      steps { sh 'mvn -B clean package' }
    }

    stage('SonarQube analysis') {
      steps {
        withSonarQubeEnv('sonarqube-25.8.0.112029') {
          sh '''
            mvn -B clean verify sonar:sonar \
              -Dsonar.host.url=$SONAR_HOST_URL \
              -Dsonar.token=$SONAR_AUTH_TOKEN \
              -Dsonar.projectKey=Maven-Build \
              -Dsonar.projectName="Maven-Build" \
              -Dsonar.sources=src/main/java,src/main/webapp \
              -Dsonar.tests=src/test/java \
              -Dsonar.java.binaries=target/classes \
              -Dsonar.java.test.binaries=target/test-classes
          '''
        }
      }
    }

    stage('Quality Gate') {
      steps {
        script {
          def qg = waitForQualityGate() // blocks until SQ returns a result
          echo "Quality Gate: ${qg.status}" // PASSED / FAILED / CANCELED / ERROR
          if (!(qg.status in ['OK','PASSED'])) {
            error "Quality Gate ${qg.status} — failing the build."
          }
          env.SONAR_QG = qg.status
        }
      }
    }

    stage('Deploy') {
      when { expression { env.SONAR_QG in ['OK','PASSED'] } }
      steps {
        echo 'Deploying because Sonar Quality Gate PASSED…'
        // deploy steps here
      }
    }
  }
}
```

