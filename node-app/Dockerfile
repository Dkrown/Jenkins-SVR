# Nodejs version 18
FROM node:18

# Working directory
WORKDIR /app

# Copy file to working directory
COPY package.json .

# Install npm
RUN npm install

# Copy file to working directory
COPY . .

# Execute the app
CMD ["npm", "start"]

# Port
EXPOSE 3000
