### `Jenkinsfile` for Jenkins Pipeline

```groovy
pipeline {
    agent any

    stages {
        stage('Install Java 17') {
            steps {
                sh '''
                    # Update the package manager and install Java 17
                    sudo yum update -y
                    sudo yum install java-17-amazon-corretto -y

                    # Verify Java installation
                    java -version
                '''
            }
        }

        stage('Clone Repository') {
            steps {
                // Clone the GitHub repository
                git branch: 'main', url: 'https://github.com/SuneelG2021/spring3_project.git'
            }
        }

        stage('Build with Maven') {
            steps {
                // Use Maven to clean and compile the project
                sh '''
                    mvn clean install
                '''
            }
        }
    }

    post {
        success {
            echo 'Build completed successfully!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
```

### How to Use This `Jenkinsfile` in Your GitHub Repository

1. **Create the Jenkinsfile in Your Repository:**
   - Navigate to your GitHub repository: `https://github.com/SuneelG2021/spring3_project.git`.
   - Create a new file named `Jenkinsfile` (with no file extension) in the root directory of your repository.
   - Copy and paste the above script into this file.
   - Commit the file to the repository.

2. **Configure Jenkins to Use the Jenkinsfile:**
   - In Jenkins, create a new Pipeline project or configure an existing one.
   - Under the `Pipeline` section, select `Pipeline script from SCM`.
   - In the `SCM` field, select `Git`.
   - Enter your repository URL: `https://github.com/SuneelG2021/spring3_project.git`.
   - Specify the branch to build (e.g., `main`).
   - In the `Script Path` field, enter `Jenkinsfile`.

3. **Save and Build:**
   - Save the configuration and go back to the project’s main page.
   - Click `Build Now` to start the pipeline. Jenkins will pull the `Jenkinsfile` from your GitHub repository and execute the pipeline stages defined in it.

### Summary
This `Jenkinsfile` will automate the process of installing Java 17, cloning your GitHub repository, and building the project using Maven. You just need to place this file in your repository, and Jenkins will automatically detect and execute it.

If you need further customization or have any additional questions, feel free to ask!
