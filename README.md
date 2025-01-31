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
