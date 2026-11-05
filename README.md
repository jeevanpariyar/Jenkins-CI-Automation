Jenkins CI Automation: Building and Executing a Java Application Using a Freestyle Project

1. Project Overview
As part of my journey in DevOps and CI/CD, I successfully configured Jenkins on an Ubuntu 24.04 LTS virtual machine hosted on Google Cloud Platform (GCP).
In this project, I created a Jenkins Freestyle project to automate the process of fetching Java source code from GitHub, compiling it, and executing the application.
During the implementation, I encountered a Git branch configuration issue that caused the initial build to fail. I investigated the Jenkins console output, identified the root cause, corrected the branch configuration, and successfully executed the Java application.
This project helped me understand how Jenkins integrates with GitHub and how CI automation works in a practical environment.
2. Technology Stack
Technology	Purpose
Google Cloud Platform	Cloud infrastructure
Ubuntu 24.04 LTS	Operating system
Jenkins	CI automation server
Git	Source code management
GitHub	Source code repository
Java	Programming language
JDK	Java compilation and execution
Freestyle Project	Jenkins job configuration
Shell Script	Build and execution commands
3. Project Architecture
The following workflow represents the CI process implemented in this project.
              Developer
                  |
                  |
             GitHub Repo
                  |
                  |
          Java Source Code
                  |
                  |
             Jenkins
                  |
           Git Integration
                  |
          Clone / Fetch Code
                  |
             Workspace
                  |
            Freestyle Job
                  |
           Execute Shell
                  |
             javac
                  |
          Compile Java Code
                  |
              java
                  |
          Execute Application
                  |
           Console Output
                  |
           Build SUCCESS
4. Step 1: Setting Up Jenkins on GCP
I created an Ubuntu 24.04 LTS virtual machine on Google Cloud Platform.
Infrastructure configuration
•	Cloud Provider: Google Cloud Platform
•	Operating System: Ubuntu 24.04 LTS
•	Application: Jenkins
•	Access: Browser-based Jenkins dashboard
•	Default Jenkins Port: 8080
After creating the VM, I connected to it using SSH.
Update Ubuntu packages
sudo apt update
sudo apt upgrade -y
Install Java
Jenkins requires Java to run.
sudo apt install fontconfig openjdk-21-jre -y
Verify Java:
java -version
Install Jenkins
Add the Jenkins repository and signing key according to the official Jenkins installation instructions.
Install Jenkins:
sudo apt update
sudo apt install jenkins -y
Start and enable Jenkins:
sudo systemctl enable jenkins
sudo systemctl start jenkins
Check service status:
sudo systemctl status jenkins
Configure firewall access
I configured the GCP firewall to allow access to Jenkins through TCP port 8080.
For a production environment, access should be restricted to trusted IP addresses or routed through a secure reverse proxy/VPN.
Access Jenkins
Open the browser:
http://YOUR_VM_EXTERNAL_IP:8080
Complete the initial Jenkins setup and create the administrator account.

 
5. Step 2: Connect Jenkins with GitHub
I created a GitHub repository containing my Java application.
GitHub Repository:
https://github.com/jeevanpariyar/Simplejavaprogram
The repository contains:
•	helloworld.java
•	Java source code for displaying a greeting message.
Why GitHub integration?
GitHub acts as the central source code repository.
Jenkins connects to GitHub to retrieve the latest source code whenever a build is triggered.
This eliminates the need to manually download source files onto the Jenkins server.
6. Step 3: Create a Freestyle Project in Jenkins
A Freestyle project is one of the basic Jenkins job types that allows us to configure build steps through the Jenkins web interface.
Job creation
1.	Log in to Jenkins as an administrator.
2.	Navigate to the Jenkins Dashboard.
3.	Click New Item.
4.	Enter the project name:
my first java jobs
5.	Select Freestyle project.
6.	Click OK.
The Jenkins job was successfully created.
Job configuration
I configured the following sections.
A. General
•	Project Name: my first java jobs
•	Project Type: Freestyle Project
B. Source Code Management
Select:
Git
Repository URL:
https://github.com/jeevanpariyar/Simplejavaprogram.git
Credentials:
None
Since this is a publicly accessible GitHub repository, authentication credentials were not required for cloning.
For private repositories, GitHub credentials should be configured securely in Jenkins Credentials.
 
 
C. Branch Specification
Initially, I configured the branch as:
*/master
However, the actual GitHub repository was using:
main
This mismatch caused the first build to fail.
After troubleshooting, I updated the branch specification to:
*/main
This allowed Jenkins to locate and check out the correct branch.
 
D. Build Triggers
I used SCM-based triggering to observe Jenkins responding to changes in the source repository.
The build log later showed:
Started by an SCM change
This indicates that Jenkins started a build in response to an SCM change.
Note: The console log confirms the SCM-triggered build, but does not establish whether the job used periodic polling or a GitHub webhook. These are separate trigger configurations.

 
E. Build Environment
No additional environment configuration was required for this basic Java application.
F. Build Steps
Select:
Add build step → Execute shell
Enter the following commands:
javac helloworld.java
java helloworld
Understanding the commands
Command 1:
javac helloworld.java
•	javac is the Java compiler.
•	It reads the Java source file.
•	It checks and compiles the source code.
•	It generates the corresponding .class bytecode file.
Command 2:
java helloworld
•	The Java launcher starts the application.
•	It loads the compiled class.
•	It executes the main method.
•	It displays the program output.
For this simple example, the Java source file contains a class named helloworld, which matches the command used to launch it.
Finally, I clicked Save.

 
7. Step 4: First Build Failure and Troubleshooting
After configuring the Jenkins job, I triggered the first build.
The initial build failed.
Error observed in Jenkins Console Output
ERROR: Couldn't find any revision to build.
Verify the repository and branch configuration for this job.

Finished: FAILURE

 
What happened?
Jenkins successfully connected to GitHub and fetched the repository information.
However, it could not find the configured branch.
The build log showed Jenkins attempting to resolve:
git rev-parse refs/remotes/origin/master^{commit}
git rev-parse origin/master^{commit}
The repository's actual default branch was main, not master.
Root Cause
The branch configured in the Jenkins job did not match the branch available in the GitHub repository.
This was a Git branch configuration issue, not a Java compilation issue.
Troubleshooting steps
1.	Opened the Jenkins job.
2.	Selected Configure.
3.	Navigated to Source Code Management.
4.	Checked the Git repository URL.
5.	Reviewed the Branch Specifier.
6.	Identified that the job was configured for master.
7.	Checked the repository and identified main as the correct branch.
8.	Updated the branch specifier.
Correct configuration:
*/main
9.	Saved the job configuration.
10.	Triggered another build.
Lesson learned
Always verify the actual branch name in GitHub before configuring the Jenkins Git branch specifier.
Modern GitHub repositories commonly use main as their default branch, although repositories can still use master or another branch name.
8. Step 5: Second Build – Successful Git Checkout
After correcting the branch configuration, I triggered another build.
Important console output
git fetch --tags --force --progress
Jenkins successfully fetched the repository.
Next:
Checking out Revision
1ab2b41a4a7aa477b408541baceedfd9a165eb7e
(refs/remotes/origin/main)
This confirmed that Jenkins located the correct branch and checked out the source code.
The build ended with:
Finished: SUCCESS
What happened internally?
1.	Jenkins connected to the GitHub repository.
2.	Git fetched the latest repository information.
3.	Jenkins identified the main branch.
4.	Jenkins checked out the latest commit.
5.	Source code was downloaded into the Jenkins workspace.
Jenkins workspace:
/var/lib/jenkins/workspace/my first java jobs
This directory is where Jenkins stores the checked-out project files and executes the configured build steps.
At this stage, the Git integration was working correctly.
9. Step 6: Execute Java Application Through Jenkins
After successfully checking out the repository, I triggered another build.
Jenkins executed the shell commands configured in the Freestyle project.
Console Output
[my first java jobs] $
/bin/sh -xe /tmp/jenkins5762630319671298498.sh

+ javac helloworld.java

+ java helloworld

Hello, World!

Finished: SUCCESS
Understanding the execution
1. Shell execution
/bin/sh -xe
Jenkins executed the configured shell script.
•	-x displays commands as they execute.
•	-e stops the script when a command fails.
2. Java compilation
javac helloworld.java
The source code was successfully compiled.
3. Java execution
java helloworld
The compiled Java application executed successfully.
4. Application output
Hello, World!
This confirmed that the Jenkins job successfully fetched, compiled, and executed the Java application.
Final result:
Finished: SUCCESS

 
10. Step 7: Test SCM-Based Build Triggering
After the manual build was successful, I made another change to the Java source code in GitHub.
The subsequent Jenkins console output showed:
Started by an SCM change
Jenkins fetched the repository again.
The log showed a new commit:
Checking out Revision
9ec9ed0d26de4e47efd57f4cbdda49adff4b97e8
(refs/remotes/origin/main)
The commit message was:
Update greeting message in helloworld.java
Jenkins then executed the same build commands:
javac helloworld.java
java helloworld
The application output was:
Hello, World!
jeevan's first POL SCM jobs on jenkins,java program
The build finished with:
Finished: SUCCESS

 

What did I learn?
This demonstrated that Jenkins could retrieve an updated commit and execute the build steps again.
It is an important part of CI automation because developers frequently update code and need those changes to be built and tested.
The log shows that the job was SCM-triggered. The exact polling or webhook mechanism should be verified separately in the job's Build Triggers configuration.
11. Understanding Other Jenkins Console Messages
During the builds, I observed some additional messages.
Message 1: Recommended Git Tool is NONE
The recommended git tool is: NONE
This was informational rather than a build failure.
Jenkins did not have a specifically configured recommended Git installation selected for the job.
However, it successfully executed Git commands using the installed Git executable.
The console confirmed:
git version 2.43.0
Therefore, Git was available and working.
Message 2: No Credentials Specified
No credentials specified
This was expected because the repository was public.
For a private repository, I would configure a GitHub Personal Access Token or another supported credential mechanism in Jenkins Credentials.
Message 3: Running as SYSTEM
Running as SYSTEM
This indicates that Jenkins executed the build under its internal SYSTEM authentication context.
It does not mean that the job was executed using my Linux administrator account.
For production environments, it is important to restrict build permissions and avoid giving untrusted jobs excessive access to the Jenkins controller or host system.
12. Final Project Results
Activity	Result
Jenkins installation on GCP	Successful
Jenkins dashboard access	Successful
GitHub repository integration	Successful
Freestyle project creation	Successful
Git branch troubleshooting	Resolved
Git checkout from main	Successful
Java source compilation	Successful
Java application execution	Successful
SCM-triggered build observed	Successful
Updated source code retrieved	Successful
Build execution summary
The project went through the following stages:
1.	Initial build: Failed because of the incorrect branch specification.
2.	Second build: Successful after changing master to main.
3.	Third build: Successful Java compilation and execution.
4.	Fourth build: Successful repeated execution.
5.	SCM-triggered build: Successfully retrieved an updated commit and executed the application.
13. Key Learnings
This hands-on exercise helped me understand several important DevOps concepts.
Jenkins: How to configure Jenkins jobs and automate application builds.
Git Integration: How Jenkins retrieves source code from GitHub.
Branch Management: Why correct branch configuration is important for CI pipelines.
Freestyle Projects: How to configure build steps through the Jenkins dashboard.
Java Compilation: How to compile Java source code using javac.
Application Execution: How to execute compiled Java applications using the Java launcher.
Troubleshooting: How to investigate Jenkins Console Output and identify the actual cause of build failures.
SCM Automation: How Jenkins can initiate builds when source code changes are detected.
14. Improvements for Future Labs
This project uses a basic Freestyle job. I plan to extend the implementation with:
•	GitHub webhook integration for event-driven builds.
•	Maven-based Java project compilation.
•	Automated unit testing.
•	Build artifact archiving.
•	Jenkins Pipeline using a Jenkinsfile.
•	GitHub integration with secure credentials.
•	Docker image creation.
•	Automated deployment to a cloud environment.
These improvements will help move from a basic CI setup towards a complete CI/CD pipeline.
15. Project References
GitHub Repository:
https://github.com/jeevanpariyar/Simplejavaprogram
Jenkins Documentation:
https://www.jenkins.io/doc/
Jenkins Freestyle Project Documentation:
https://www.jenkins.io/doc/book/using/using-projects/
Jenkins Git Plugin:
https://plugins.jenkins.io/git/
Jenkins SCM Polling Documentation:
https://www.jenkins.io/doc/book/pipeline/syntax/
________________________________________
LinkedIn Project Summary
This project demonstrates my practical experience setting up Jenkins on GCP, integrating GitHub, creating a Freestyle job, and troubleshooting a real build failure.
The most valuable part of this exercise was not just getting a successful build, but understanding why the first build failed and how to fix it using Jenkins Console Output.



