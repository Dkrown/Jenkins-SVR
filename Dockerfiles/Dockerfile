# Use the official Jenkins LTS image with JDK 17
FROM jenkins/jenkins:lts-jdk17

# Set user to root for installation
USER root

# Install dependencies
RUN apt-get update && apt-get install -y lsb-release

# Add Docker’s official GPG key
RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
  https://download.docker.com/linux/debian/gpg

# Add Docker repository
RUN echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
  https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list

# Install Docker CLI
RUN apt-get update && apt-get install -y docker-ce-cli

# Pre-install essential Jenkins plugins
RUN jenkins-plugin-cli --plugins docker-workflow pipeline-stage-view blueocean

# Switch back to Jenkins user
USER jenkins

# Expose necessary ports
EXPOSE 8080 50000

# Set volume for persistence
VOLUME ["/var/jenkins_home"]

# Start Jenkins
CMD ["jenkins.sh"]
