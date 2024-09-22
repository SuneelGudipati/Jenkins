Here's how you can set up a Jenkins pipeline for a GitHub repository using Jenkins's graphical user interface without manually writing a script:

### Setting Up a Jenkins Pipeline for a GitHub Repository Without Writing a Script

This guide provides a detailed step-by-step process for setting up a Jenkins pipeline using Jenkins’s GUI to connect it to a GitHub repository. We'll use the repository `https://github.com/SuneelG2021/spring3_project.git` as an example.

---

#### 1. **Launch an EC2 Instance**
1. **Create an EC2 Instance:**
   - Log in to your AWS Management Console and launch a new EC2 instance using Amazon Linux 2 or your preferred Linux distribution.
   - Select an instance type (e.g., `t2.micro` for testing purposes).
   - Configure the instance and security groups, ensuring you allow traffic on port 22 (SSH) and port 8080 (Jenkins).

2. **Connect to Your EC2 Instance:**
   - Use SSH to connect to your EC2 instance:
     ```bash
     ssh -i your-key.pem ec2-user@your-ec2-public-ip
     ```

---

#### 2. **Install Jenkins on EC2**
1. **Add Jenkins Repository:**
   - Download the Jenkins repository to your EC2 instance:
     ```bash
     sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
     ```

2. **Import Jenkins GPG Key:**
   - Import the GPG key to verify package authenticity:
     ```bash
     sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
     ```

3. **Update Yum Packages:**
   - Update your package manager to ensure all repositories are synchronized:
     ```bash
     sudo yum update -y
     ```

4. **Install Jenkins:**
   - Install Jenkins using the Yum package manager:
     ```bash
     sudo yum install jenkins -y
     ```

---

#### 3. **Install Java 17 and Fontconfig**
1. **Install Java 17 and Fontconfig:**
   - Jenkins requires Java to run, and `fontconfig` is required for some plugins and dependencies:
     ```bash
     sudo yum install fontconfig java-17-openjdk -y
     ```

2. **Verify Java Installation:**
   - Check the installed Java version to confirm the installation:
     ```bash
     java -version
     ```

---

#### 4. **Configure Java Environment Variables**
1. **Set JAVA_HOME and PATH:**
   - Configure the JAVA_HOME and PATH variables to ensure Jenkins can locate Java:
     ```bash
     echo "export JAVA_HOME=$(dirname $(dirname $(readlink $(readlink $(which java)))))" | sudo tee -a /etc/profile
     echo "export PATH=$PATH:$JAVA_HOME/bin" | sudo tee -a /etc/profile
     source /etc/profile
     ```

2. **Verify JAVA_HOME:**
   - Ensure the JAVA_HOME environment variable is set correctly:
     ```bash
     echo $JAVA_HOME
     ```

---

#### 5. **Start Jenkins**
1. **Start Jenkins Service:**
   - Start the Jenkins service:
     ```bash
     sudo systemctl start jenkins
     ```

2. **Enable Jenkins at Boot:**
   - Ensure Jenkins starts automatically after a reboot:
     ```bash
     sudo systemctl enable jenkins
     ```

3. **Check Jenkins Status:**
   - Verify Jenkins is running without issues:
     ```bash
     sudo systemctl status jenkins
     ```

---

#### 6. **Adjust Security Group or Firewall Settings**
1. **Allow Jenkins Port (8080):**
   - Open port 8080 in your security group settings in AWS, or use the following commands for firewalls on the instance:
     ```bash
     sudo firewall-cmd --permanent --zone=public --add-port=8080/tcp
     sudo firewall-cmd --reload
     ```

---

#### 7. **Access Jenkins and Complete Setup**
1. **Access Jenkins:**
   - Open a browser and navigate to:
     ```
     http://<your-ec2-public-ip>:8080
     ```

2. **Unlock Jenkins:**
   - Retrieve the initial admin password and enter it in the Jenkins setup page:
     ```bash
     sudo cat /var/lib/jenkins/secrets/initialAdminPassword
     ```

3. **Install Suggested Plugins:**
   - Select "Install suggested plugins" to quickly set up Jenkins with the most commonly used plugins.

4. **Create Admin User:**
   - Follow the prompts to create your admin user account and complete the initial setup.

---

#### 8. **Install and Configure Maven**
1. **Download Maven:**
   - Download the latest version of Maven:
     ```bash
     wget https://dlcdn.apache.org/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.tar.gz
     ```

2. **Extract Maven:**
   - Extract the downloaded archive to the `/opt` directory:
     ```bash
     sudo tar -xvzf apache-maven-3.9.9-bin.tar.gz -C /opt/
     ```

3. **Rename Maven Folder:**
   - Rename the Maven directory for easier reference:
     ```bash
     sudo mv /opt/apache-maven-3.9.9 /opt/maven
     ```

4. **Set Maven Environment Variables:**
   - Add Maven to your system path:
     ```bash
     echo "export M2_HOME=/opt/maven" | sudo tee -a /etc/profile
     echo "export M2=$M2_HOME/bin" | sudo tee -a /etc/profile
     echo "export PATH=$PATH:$M2" | sudo tee -a /etc/profile
     source /etc/profile
     ```

5. **Verify Maven Installation:**
   - Check the Maven version to confirm the installation:
     ```bash
     mvn -version
     ```

---

#### 9. **Install Git**
1. **Install Git:**
   - Install Git, which is essential for source code management:
     ```bash
     sudo yum install git -y
     ```

2. **Verify Git Installation:**
   - Confirm the installation by checking the version:
     ```bash
     git --version
     ```

---

#### 10. **Configure Jenkins for Java, Maven, and Git**
1. **Open Jenkins Dashboard:**
   - Navigate to the Jenkins home page and log in.

2. **Navigate to Jenkins Configuration:**
   - Go to `Manage Jenkins` from the sidebar.

3. **Access Global Tool Configuration:**
   - Click on `Manage Jenkins` -> `Global Tool Configuration`.
   - This is where you configure the JDK, Maven, and Git installations.

4. **Configure JDK:**
   - Under the `JDK` section, click `Add JDK` and uncheck the "Install automatically" option.
   - Enter the name (e.g., `JDK 17`) and the path to the JDK (e.g., `/usr/lib/jvm/java-17-openjdk`).

5. **Configure Maven:**
   - Under the `Maven` section, click `Add Maven`.
   - Enter the name (e.g., `Maven 3.9.9`) and the path to Maven (e.g., `/opt/maven`).

6. **Configure Git:**
   - Under the `Git` section, click `Add Git` and provide the Git executable path (e.g., `/usr/bin/git`).

---

#### 11. **Setting Up a Jenkins Pipeline for GitHub**

1. **Create a New Pipeline Project:**
   - Go to the Jenkins Dashboard and click on `New Item`.
   - Enter a name, e.g., `MyFirstPipeline`.
   - Select `Freestyle Project` instead of `Pipeline` and click `OK`.

2. **Configure the Source Code Management (SCM):**
   - Under the `Source Code Management` section, select `Git`.
   - Provide the repository URL: `https://github.com/SuneelG2021/spring3_project.git`.
   - Specify the branch to build, for example, `*/main`.

3. **Set Up GitHub Credentials:**
   - If your repository is private, click on `Add` next to `Credentials` and enter your GitHub credentials.
   - Use these credentials in the `Credentials` dropdown for SCM.

4. **Define Build Steps Using the "Build" Section:**
   1. **Add Build Steps:**
      - Scroll down to the `Build` section.
      - Click on `Add build step` and choose:
        - **Invoke top-level Maven targets:** For Maven-based builds. Set the goal as `clean install`.
        - **Execute shell:** For shell commands. For example, you can use:
        ```bash
        mvn clean compile
        ```

5. **Configure Post-Build Actions:**
   - Scroll down to the `Post-build Actions` section.
   - Click `Add post-build action` and choose options like `Archive the artifacts` or `Email Notification`.

6. **Configure Build Triggers (Optional):**
   - To automatically trigger the build on code changes, scroll up to `Build Triggers`.
   - Check `GitHub hook trigger for GITScm polling`.
   - Configure a webhook in your GitHub repository settings to trigger the build.

7. **Save and Build the Project:**
   - Click `Save` to store the configuration.
   - Go to

 the project’s main page and click `Build Now` to manually start the build.

8. **Monitor and View Build Results:**
   - Click on the build number in the `Build History` to view the `Console Output` for logs.
   - Check for the success or failure status of your build.

---

### Summary
This guide provides a step-by-step process to set up and run a Jenkins pipeline for a GitHub repository without using a pipeline script, connected to the GitHub repository `https://github.com/SuneelG2021/spring3_project.git`. The configuration is done entirely through the Jenkins GUI, making it accessible for users without scripting knowledge.
