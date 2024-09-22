Here's how you can set up a Jenkins pipeline for a GitHub repository using Jenkins's graphical user interface with manually writing a script:

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
   - Jenkins requires Java to run, 
     ```bash
     sudo yum install java-17-amazon-corretto -y
     ```
     (or `fontconfig` is required for some plugins and dependencies:)
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

## Step 1: Create a New Pipeline Project
1. Go to the **Jenkins Dashboard**.
2. Click on **New Item**.
3. Enter a name (e.g., `MyFirstPipeline`).
4. Select **Pipeline** as the project type and click **OK**.

## Step 2: Configure the Source Code Management (SCM)
1. In the **Source Code Management** section, select **Git**.
2. Provide the **repository URL** and specify the branch (e.g., `*/main`).

## Step 3: Configure the Pipeline Script
1. In the **Pipeline** section, select **Pipeline script** and input your pipeline script in Groovy:

    ```groovy
    pipeline {
        agent any
        stages {
            stage('Checkout') {
                steps {
                    git branch: 'main', url: 'https://github.com/SuneelG2021/spring3_project.git'
                }
            }
            stage('Build') {
                steps {
                    echo 'Building...'
                    sh 'mvn clean compile'
                }
            }
            stage('Test') {
                steps {
                    echo 'Testing...'
                    sh 'mvn test'
                }
            }
            stage('Package') {
                steps {
                    echo 'Packaging...'
                    sh 'mvn package'
                }
            }
            stage('Deploy') {
                steps {
                    echo 'Deploying...'
                    // Add your deployment script here
                }
            }
        }
    }
    ```

2. This script performs checkout, build, test, package, and deployment stages.

## Step 4: Save the Configuration
1. Click the **Save** button to store your pipeline configuration.

## Step 5: Trigger the Pipeline
1. Go to the project’s main page and click **Build Now** to start the pipeline manually.
2. For automatic builds, configure triggers in the **Build Triggers** section.

## Step 6: Monitor the Pipeline
1. Check the **Console Output** for real-time logs.
2. View the **Pipeline visualization** for stage results.

## Conclusion
Following these steps will help you set up and run a Jenkins Pipeline effectively, allowing for streamlined CI/CD processes in your projects.
