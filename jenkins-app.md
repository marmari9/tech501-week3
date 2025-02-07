# How to create a CICD pipeline:


## why build a pipeline:
- Makes devs life easier
- quickly get changes to end users
- reduce risk that big code changes are going to mess things up
- Buisness value: saves time and money. Users are actually using the latest changes/features as soon as code changes happen.


# why use jenkins over other tools:
- jenkins is not a CICD tool it is a automation server
- free, open source
- powerful plugins

**Contin delivery is in a testing environment while conti deployment is a production, the delivery provides approval for next stage**


## how to build a pipeline:
- on jenkins create new item
- name the item and select free style project
- click on the project and change the environment to bash 
- upload your code (eg, uname -a)
- chack if the pipline is built


![alt text](image.png)



## Step 1 : Create job1 on jenkins and connect to GitHub repo

- generate ssh key pairs and name it: maram-jenkins-to-github-saap
- on GitHub, go to the cicd-sparta-app-cicd repo and add the public key by going to the settings and from security deply a key and paste the public key. 
- on jenkins create job 1 by following these steps:
  - **create new item** (maram-job1-ci-test)
  - **Discard old builds**: set to 5 
  - check **GitHub project** and add the https repo url : https://github.com/marmari9/tech501-maram-sparta-app-cicd/
  - under **source code management**: 
    - select **git** and add the ssh repo url: git@github.com:marmari9/tech501-maram-sparta-app-cicd.git
    - create **new credintials** and add the private key
    - On braches to build: change to */main
    - Under **Build Environment**, check **provide Node and npm bin/folder to PATH**, select NodeJS version 20.
    - select secure bash shell and run the following:
    ```bash 
      cd app 
      npm install 
      npm test
      ``` 
- save changes
- **start build**
- check status and console output

![alt text](<Screenshot 2025-02-06 144129.png>)


# Jenkins Job 2: Git Checkout and Merge

## Step 1: Create a New Job in Jenkins

1. Go to your **Jenkins Dashboard**.
2. Click **New Item** on the left sidebar.
3. Enter a name for your job (e.g., `maram-job2-ci-merge`).
4. Select **Freestyle project** and click **OK**.

## Step 2: Configure Source Code Management

1. Scroll down to the **Source Code Management** section.
2. Select **Git**.
3. In the **Repository URL**, enter the Git repository URL (e.g., `git@github.com:marmari9/tech501-maram-sparta-app-cicd.git`).
4. Under **Credentials**, select the appropriate Git credentials..
5. In **Branch Specifier**, enter the branch name you want to work with (e.g., `main`).

## Step 3: Add Build Step to Checkout and Merge

1. Scroll to the **Build** section.
2. Click **Add build step** and select **Execute shell**.
3. In the **Command** field, enter the following shell script to checkout and merge the code:

    ```bash
        git checkout main
        git pull origin main
        git merge --ff-only origin/dev
        git push origin main
    ```

   This script will:
   - Checkout the `main` branch.
   - Fetch the latest changes from the remote repository.
   - Merge the latest changes from `origin/main` into the local `main` branch.


## Step 4 : Save the Job

1. Once you've configured everything, click **Save** to create the job.

## Step 5: Trigger the Job

1. Manually trigger the job by clicking **Build Now**.
2. Jenkins will run the build step, fetch the latest code, and merge it.

---

### Notes:

- Ensure that Jenkins has access to your Git repository via the correct credentials (SSH key).
- You may need to troubleshoot if any issues arise with the Git commands (e.g., permission issues, merge conflicts).


# Jenkins Job 3: Continuous Deployment

This guide will show you how to create a Jenkins Job for **Job 3** that automates the deployment process using SCP and SSH to deploy your app to an EC2 instance.

## Steps to Create Jenkins Job 3

### 1. Open Jenkins Dashboard
- Navigate to your Jenkins web interface.

### 2. Create a New Job
- Click on **"New Item"** at the left-hand side of the Jenkins dashboard.
- In the **"Item name"** field, enter a name for your job (e.g., `maram-job3-cd-deploy`).
- Select **"Freestyle project"** and click **OK**.

### 3. Configure Source Code Management
- Go to the **"Source Code Management"** section.
- Select **Git**.
- Enter your repository URL (e.g., `git@github.com:marmari9/tech501-maram-sparta-app-cicd.git`).
- Under **Credentials**, select the Git credentials.

### 4. Set Build Triggers
- Go to the **"Build Triggers"** section.
- Choose an option (**GitHub hook trigger for GITScm polling**).

### 5. Add Build Step
- Scroll down to the **"Build"** section.
- Click on **"Add build step"** and select **"Execute shell"**.

### 6. Insert the SCP and SSH Commands
In the **"Command"** text box, paste the following:

```bash
# Copy the app files to EC2
scp -i /var/jenkins/keys/maram-key-3.pem -r /var/jenkins/workspace/maram-job3-cd-deploy/app/* ubuntu@3.255.128.209:/app/

# Install dependencies and restart the app on EC2
ssh -i /var/jenkins/keys/maram-key-3.pem ubuntu@3.255.128.209 "cd /app"
``` 

* Blockers : I had issues with the ssh agent permisshions:


![](<Screenshot 2025-02-07 164742.png>)


- tried to fix using these codes:
```bash 
scp -o StrictHostKeyChecking=no -r /var/jenkins/workspace/maram-job2-ci-merge/app ubuntu@3.255.128.209:/home/ubuntu/repo
ssh ubuntu@3.255.128.209 << 'EOF'
	
	rm -rf /home/ubuntu/repo/app
	Change to the newly copied app directory
	cd /home/ubuntu/repo/app
	ls
	sudo systemctl restart nginx
	npm install 
  npm start
	pm2 start app.js
EOF
```
- still having issues with the ssh agent.

![alt text](2.png)