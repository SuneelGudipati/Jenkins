# Setting Up a Jenkins Pipeline Without Writing a Script for GitHub Repository

This guide will help you set up a Jenkins pipeline using the graphical user interface (GUI) without manually writing a script. We will connect to the GitHub repository `https://github.com/SuneelG2021/spring3_project.git`.

---

## 1. Launch an EC2 Instance

### Create an EC2 Instance
- Log in to your AWS Management Console and launch a new EC2 instance using Amazon Linux 2 or your preferred Linux distribution.
- Choose an instance type (e.g., `t2.micro` for testing).
- Ensure your security group allows traffic on port 22 (SSH) and port 8080 (Jenkins).

### Connect to Your EC2 Instance
Use SSH to connect:
```bash
ssh -i your-key.pem ec2-user@your-ec2-public-ip
```

## 2. Install Jenkins on EC2

### Add Jenkins Repository
```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
```

### Import Jenkins GPG Key
```bash
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
```

### Update Yum Packages
```bash
sudo yum update -y
```

### Install Jenkins
```bash
sudo yum install jenkins -y
```

---

## 3. Install Java 17 (OpenJDK)

### Install Java 17
```bash
sudo yum install java-17-amazon-corretto -y
```

### Verify Java Installation
```bash
java -version
```

---

## 4. Configure Java Environment Variables

### Set JAVA_HOME and PATH
```bash
echo "export JAVA_HOME=$(dirname $(dirname $(readlink $(readlink $(which java)))))" | sudo tee -a /etc/profile
echo "export PATH=\$PATH:\$JAVA_HOME/bin" | sudo tee -a /etc/profile
source /etc/profile
```

### Verify JAVA_HOME
```bash
echo $JAVA_HOME
```

---

## 5. Start Jenkins

### Start Jenkins Service
```bash
sudo systemctl start jenkins
```

### Enable Jenkins at Boot
```bash
sudo systemctl enable jenkins
```

### Check Jenkins Status
```bash
sudo systemctl status jenkins
```

---

## 6. Adjust Security Group or Firewall Settings

### Allow Jenkins Port (8080)
- Open port 8080 in your security group settings in AWS or use the following commands for firewalls:
```bash
sudo firewall-cmd --permanent --zone=public --add-port=8080/tcp
sudo firewall-cmd --reload
```

---

## 7. Access Jenkins and Complete Setup

### Access Jenkins
Open a browser and navigate to:
```
http://<your-ec2-public-ip>:8080
```

### Unlock Jenkins
Retrieve the initial admin password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### Install Suggested Plugins
Select "Install suggested plugins" to set up Jenkins with commonly used plugins.

### Create Admin User
Follow the prompts to create your admin user account and complete the setup.

---

## 8. Install and Configure Maven

### Download Maven
```bash
wget https://dlcdn.apache.org/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.tar.gz
```

### Extract Maven
```bash
sudo tar -xvzf apache-maven-3.9.9-bin.tar.gz -C /opt/
```

### Rename Maven Folder
```bash
sudo mv /opt/apache-maven-3.9.9 /opt/maven
```

### Set Maven Environment Variables
```bash
echo "export M2_HOME=/opt/maven" | sudo tee -a /etc/profile
echo "export M2=\$M2_HOME/bin" | sudo tee -a /etc/profile
echo "export PATH=\$PATH:\$M2" | sudo tee -a /etc/profile
source /etc/profile
```

### Verify Maven Installation
```bash
mvn -version
```

---

## 9. Install Git

### Install Git
```bash
sudo yum install git -y
```

### Verify Git Installation
```bash
git --version
```

---

## 10. Configure Jenkins for Java, Maven, and Git

### Open Jenkins Dashboard
Navigate to the Jenkins home page and log in.

### Navigate to Jenkins Configuration
Go to `Manage Jenkins` from the sidebar.

### Access Global Tool Configuration
- Click on `Manage Jenkins` -> `Global Tool Configuration`.
- Configure JDK, Maven, and Git installations.

### Configure JDK
- Under the `JDK` section, click `Add JDK` and uncheck "Install automatically".
- Enter a name (e.g., `JDK 17`) and the path to the JDK (e.g., `/usr/lib/jvm/java-17-amazon-corretto`).

### Configure Maven
- Under the `Maven` section, click `Add Maven`.
- Enter a name (e.g., `Maven 3.9.9`) and the path to Maven (e.g., `/opt/maven`).

### Configure Git
- Under the `Git` section, click `Add Git` and provide the Git executable path (e.g., `/usr/bin/git`).

---

## 11. Running a Jenkins Pipeline Without a Script

### Create a New Pipeline Project
- Go to the Jenkins Dashboard and click `New Item`.
- Enter a name (e.g., `MyFirstPipeline`) and select `Freestyle Project`, then click `OK`.

### Configure the Source Code Management (SCM)
- Under `Source Code Management`, select `Git`.
- Provide the repository URL: `https://github.com/SuneelG2021/spring3_project.git`.
- Specify the branch to build (e.g., `*/main`).

### Define Build Steps
1. **Add Build Steps:**
   - Scroll to the `Build` section.
   - Click `Add build step` and choose:
     - **Invoke top-level Maven targets:** Set the goal as `clean install`.
     - **Execute shell:** For shell commands like:
     ```bash
     mvn clean compile
     ```

2. **Configure Post-Build Actions:**
   - In the `Post-build Actions` section, choose options like `Archive the artifacts` or `Email Notification`.

### Save and Build the Project
- Click `Save` to store the configuration.
- On the project’s main page, click `Build Now` to start the build.

### Monitor and View Build Results
- Click on the build number in the `Build History` to view the `Console Output` for logs and check the build status.

---

### Summary
This guide outlines the steps to set up a Jenkins pipeline using the GUI, connected to the GitHub repository `https://github.com/SuneelG2021/spring3_project.git`. You can configure Jenkins to build and manage your projects seamlessly without writing any pipeline scripts.
```

You can add this content to your repository, and it will be formatted nicely when viewed on GitHub. Let me know if you need further modifications!
