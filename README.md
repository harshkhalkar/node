# > Deploying a Node.js Application Using Jenkins (Freestyle Project)

## 1. Install Node.js Plugin in Jenkins
To run a Node.js app, first install the NodeJS plugin from Jenkins Plugin Manager.

## 2. Configure Node.js in Jenkins
- Navigate to:
    
    `Manage Jenkins → Tools → NodeJS Installation`
    
- Click Add NodeJS
    - Name the installation (e.g., `NodeJS 18`)

## 3. Configure SSH Credentials and SSH Site
- Go to:
    
    `Manage Jenkins → Credentials → System → Add Credentials`
    
    - Kind: SSH Username with private key
    - Provide your username and private key
- Now, go to:
    
    `Manage Jenkins → System`
    
    Scroll down to SSH Sites
    
    - Add Hostname, Port, and select the credential you just added.

## 4. Create a New Jenkins Job
- Click New Item
    - Item Name: `mynode-app`
    - Project Type: Freestyle Project
 
## 5. Configure Source Code Management (SCM)
- In the job configuration:
    - Add a short Description
    - Scroll down to Source Code Management
        - Select Git
        - Paste your repository URL
     
## 6. Configure Build Triggers
- Under Build Triggers, select:
    
    `GitHub hook trigger for GITScm polling`

## 7. Configure Build Environment
- Under Build Environment:
    - Check Provide Node & npm bin/folder to PATH
        - Select the NodeJS version you configured earlier
    - Enable SSH Agent
        - Select the SSH credential you added
     
## 8. Add Build Step
- Add Build Step:
    
    `Execute shell script on remote host using ssh`
    
    - Choose SSH Site
    - Add the following command:
 
# For Non-Containerized App:
```bash
sudo yum install nodejs npm -y
npm install -g pm2
mkdir -p /node
git init
git pull <github-repo-link>
sudo npm install
pm2 start index.js || pm2 restart index.js
```

# For Containerized App (Dockerfile):
```bash
mkdir -p /node
git init
git pull <github-repo-link>
sudo docker build -t myapp .
sudo docker run -d -p 3000:3000 --name nodeapp1 myapp
```
---
# Note: Give Jenkins permission to run Docker commands:
```bash
sudo usermod -aG docker jenkins

# OR

sudo visudo
# Add the following line:
jenkins ALL=(ALL) NOPASSWD:ALL
```

## 9. Setup GitHub Webhook
- In GitHub repository:
    - Go to `Settings → Webhooks → Add Webhook`
    - Payload URL: `http://<jenkins-ip>:8080/github-webhook/`
    - Save the webhook
 
## 10. Push Code and Trigger Build
- Push your code to GitHub. Jenkins will automatically trigger a build via the webhook.

## 11. Access the App
- Use your server IP and port to access the app:
    
    `http://<server-ip>:3000/`

---
# Deploying a Node.js Application Using Jenkins (Freestyle Project)
- Jenkins Home Page
    - New Item
        - Name: `NodeJS-pipeline`
        - Type: Select Pipeline
        - Click OK
- General
    - Description: *(Optional)* Write a brief description of the pipeline job.
- Build Triggers
    - Turn on GitHub hook trigger for GITScm polling

**Same as Freestyle Project**
---

# Additional 
## NOTE - If not SSH agent then how?
Perform SSH manually in execute shell - 

Make sure to copy .pem file to jenkins server. 

`scp -i /path/of/.pem /live/server/.pem ubuntu@<ip>:location-to-pate`

additionally give correct permissions set to the `.pem` file (`chmod 400`).

Now, Execute Shell
`ssh -i /location/of/.pem -o StrictHostKeyChecking=no ec2-user@<public-ip> <<EOF`
For Pipeline Project, Navigate to  pipeline syntax and choose ssh agent and choose ceredential that are added. 
```bash
script {
sshagent(['b1e89f3b-683b-42ff-a2ba-7ce']){
                sh '''
                    ssh -o StrictHostKeyChecking=no ec2-user@<livesrver-public-ip> << 'ENDSSH'
                    //some commands
                ```
                }           # Add sh ``` to write commands and use ssh
}
```
