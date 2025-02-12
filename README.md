# Setting up a Jenkins-Docker container

This 'READ.me' file is about how to set up a Jenkins-Docker container with either Jenkins 'Built-In' node
or with Jenkins 'docker-agent' as a best practice.

The two 'docker-compose.yml' files contain one with '.NoAGENT' which the default Jenkins 'Built-In' node
while the other ending with '.yml' is configured as an agent [docker-agent].

Note: the docker-compose.yml file is in the main-directory [Jenkins-SVR] while the Dockerfile is in sub-directory 
[Jenkins-SVR/Dockerfiles]. And this docker command: 'docker compose up -d --build' should run from the 
main directory [Jenkins-SVR]. IMPORTANT: If using these files [Dockerfile and docker-compose.yml], these two
directories (main-directory [Jenkins-SVR] and [Jenkins-SVR/Dockerfiles]) must be created as these directories [path]
were included in the 'docker-compose.yml' file.

In addition, first run Jenkins with the 'Built-In' node and set up the 'docker-agent' node, save it and check the
status to extract [copy] the 'secret' key and add it to the 'docker-compose' file configured for the 'docker-agent'.
As the Jenkins 'docker-agent' needed this to function/operate.

Steps:
To copy the 'secret' key in Jenkins:
-----------------------------------------
Go to Jenkins Dashboard → Manage Jenkins → Manage Nodes
Click New Node → Enter a name like docker-agent → Select Permanent Agent
Configure:
---------
Number of Executors: 1
Remote Root Directory: /home/jenkins/agent
Launch method: Select "Launch agent by connecting it to the master"
Work Directory: /home/jenkins/agent
Save → Copy the Secret token for the agent.
Replace <your-agent-secret> in docker-compose.yml with this Secret Token.

Disable Builds on the Built-in Node
-----------------------------------
Go to Jenkins Dashboard → Manage Jenkins → Manage Nodes
Click on Built-In Node
Click Configure
Change "Number of Executors" from 2 to 0
Click Save

Restart Jenkins with the Agent:
------------------------------
docker compose up -d --build
docker compose ps

Verify Jenkins Agent Connection:
-------------------------------
Go to Jenkins Dashboard → Manage Jenkins → Manage Nodes
You should see docker-agent (Connected, Idle)
Try running a test job on the agent.

What Happens Next?
-----------------
Jenkins Controller (Built-in Node) → Will still manage jobs but won’t run builds.
Jenkins Agent (docker-agent) → Will now handle all builds.
===============================================================================
+++ This keeps Jenkins secure and optimized, preventing performance issues. +++
===============================================================================

Bonus:
To push to 'github' using SSH follow these steps
------------------------------------------------
Configure Git for GitHub:
-------------------------
git config --global user.name "YourGitHubUsername"
git config --global user.email "youremail@example.com"

Generate SSH Key (if not already set up):
----------------------------------------
ssh-keygen -t rsa -b 4096 -C "youremail@example.com"

Copy the SSH key and add it to GitHub:
cat ~/.ssh/id_rsa.pub

Go to GitHub → Settings → SSH Keys → New SSH Key → Paste the key.

Test GitHub SSH Connection:
-------------------------
ssh -T git@github.com

Switch Git to Use SSH for the Remote Repository:
-----------------------------------------------
git remote set-url origin git@github.com:YourGitHubUsername/Jenkins-SVR.git

Verify the change with this command: git remote -v
It should show something like these:
	origin  git@github.com:YourGitHubUsername/Jenkins-SVR.git (fetch)
	origin  git@github.com:YourGitHubUsername/Jenkins-SVR.git (push)

Initialize Git in the Local Directory:
-------------------------------------
cd ~/Jenkins-SVR
git init

Add the GitHub repository as the remote origin:
git remote add origin git@github.com:YourGitHubUsername/Jenkins-SVR.git

Stage and Commit Files:
----------------------
Stage all files in the directory:	git add .
Commit the files with a message:	git commit -m "Initial commit - Jenkins Docker Setup"
Push the Directory to GitHub:		git branch -M main
					git push -u origin main

Verify on GitHub:
----------------
Go to your GitHub repository (https://github.com/YourGitHubUsername/Jenkins-SVR).
Refresh the page—you should see your Jenkins-SVR files uploaded.


SSH authentication issue when Jenkins is trying to fetch your GitHub repository:
=============================================================================
"Host key verification failed. Fatal: Could not read from remote repository."
=============================================================================
This means Jenkins is unable to verify GitHub's SSH key, preventing access to the repository.

Steps to fix the issue:
=======================
If jobs run in 'jenkins-agent', run this command: docker exec -it jenkins-agent bash
Then, inside the container, test SSH access to GitHub: ssh -T git@github.com
If prompted authentication failed with: Permission denied (publickey).
You need to configure SSH keys inside the container as demonstrated below:

Log into your Jenkins container [or server]:
--------------------------------------------
docker exec -it jenkins-agent bash

Generate SSH key pair:
----------------------
ssh-keygen -t rsa -b 4096 -C "whatever-name"

Add/copy the SSH key to GitHub:
-------------------------------
cat ~/.ssh/id_rsa.pub

Ensure Jenkins can read the key:
--------------------------------
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chmod 700 ~/.ssh

Ensure SSH Agent is Running and Using the Key:
----------------------------------------------
eval $(ssh-agent -s)  # Start SSH agent
ssh-add ~/.ssh/jenkins-agent-github  # Add the SSH key

Or start the SSH agent manually:
--------------------------------
eval `ssh-agent`
ssh-add ~/.ssh/jenkins-agent-github

Or running the SSH command explicitly with your private key:
------------------------------------------------------------
ssh -i ~/.ssh/jenkins-agent-github -T git@github.com

Create SSH Config File:
-----------------------
echo "Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/jenkins-agent-github
  IdentitiesOnly yes" > ~/.ssh/config

Set Correct Permissions:
------------------------
chmod 600 ~/.ssh/config

Verify SSH Authentication:
--------------------------
ssh -T git@github.com

If everything is correct, it should return:
-------------------------------------------
Hi Dkrown! You've successfully authenticated, but GitHub does not provide shell access.

Restart Jenkins & Retry Pipeline:
---------------------------------
docker compose down
docker compose up -d
