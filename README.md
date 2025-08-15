# IntegrateSonarwithJenkins
# Install Jenkins in AWS EC2 
#### Note: Use instance type - t2.medium  
Once the Instance is created, follow the steps below  
    1. First update the system.   
    
        ```
        sudo apt update
        ```

        
    2. install jenkins  
        
        ``` bash
        sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
        https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
        echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
        https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
        /etc/apt/sources.list.d/jenkins.list > /dev/null
        sudo apt-get update
        sudo apt-get install jenkins
        ```

