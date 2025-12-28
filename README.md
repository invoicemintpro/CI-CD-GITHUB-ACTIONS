🚀 GitHub to VPS Deployment Guide using Git + Aapanel + GitHub Actions
📂 Login to Aapanel and Set Up Project Directory
Log in to your Aapanel to manage your web server environment. Navigate to the project directory where you want to deploy your application.

✅ Step 1: Install and Configure Git on VPS
Check if Git is Installed
Run the following command on your VPS to check if Git is installed:

git
Install Git (If Not Installed)
If Git is not installed, run:

sudo apt install git -y
🔑 Step 2: Generate SSH Key on VPS
Generate an SSH key pair on your VPS:

ssh-keygen -t rsa -b 4096 -C "your_email@example.com" -f ~/.ssh/ajmain
Press Enter when prompted to confirm the path. You’ll get two files:

Private key: ~/.ssh/ajmain
Public key: ~/.ssh/ajmain.pub
📋 Step 3: Copy & Register the SSH Public Key
Append the public key to authorized keys:
cat ~/.ssh/ajmain.pub >> ~/.ssh/authorized_keys
Display the public key:
cat ~/.ssh/ajmain.pub
Go to GitHub → Settings → SSH and GPG keys
Click "New SSH Key"
Title: VPS Public Key
Paste the copied key
Save
Test the SSH Connection
If your SSH key is named id_rsa, run:
ssh -T git@github.com
If you have multiple SSH keys, specify the key explicitly:
ssh -T -i ~/.ssh/ajmain git@github.com
A successful connection will display a message like:

Hi username! You've successfully authenticated, but GitHub does not provide shell access.
🌐 Step 4: Clone Repository to VPS
For Single User Repositories:
cd /www/wwwroot/test.mirpurianscafe.com
git clone git@github.com:MahfujuRahman/cd.git .
For Multiple User Repositories:
One-Time Setup: Configure SSH for Multiple Keys
If you have multiple GitHub accounts or SSH keys, configure SSH to use the correct key for each repository. Edit or create the SSH config file:

nano ~/.ssh/config
Add the following configuration:

Host github.com-ajmain
  HostName github.com
  User git
  IdentityFile ~/.ssh/ajmain
Clone the Repository Using the Alias:
cd /www/wwwroot/test.mirpurianscafe.com
git clone git@github.com-ajmain:MahfujuRahman/Superb-Backend.git .
Test the SSH Connection:
ssh -T git@github.com-ajmain
A successful connection will display a message like:

Hi username! You've successfully authenticated, but GitHub does not provide shell access.
🛡️ Step 4.1: Configure Git Safe Directory
After cloning the repository, you might need to mark the project directory as a safe directory to avoid Git warnings. Use the following commands:

git config --global --add safe.directory /www/wwwroot/your-project-path
Replace /www/wwwroot/your-project-path with the actual path to your project directory on the VPS.

This step ensures that Git operations can be performed securely within the specified directories.

🛠️ Step 5: Add GitHub Actions Workflow File
In your project folder, create the file:

.github/workflows/deploy.yml

name: CICD TEST

on:
  push:
  branches: [main]

jobs:
  build:
  runs-on: ubuntu-latest

  steps:
    - name: Deploy using ssh
    uses: appleboy/ssh-action@master
    with:
      host: ${{ secrets.VPS_HOST }}
      username: ${{ secrets.VPS_USER }}
      key: ${{ secrets.SSH_PRIVATE_KEY }}
      port: 22
      script: |
      cd ${{ secrets.PROJECT_PATH }}
      git reset --hard HEAD
      git clean -fd
      git pull origin master --prune --force || true
🔐 Step 6: Add GitHub Secrets
Go to your GitHub repository → Settings → Secrets and variables → Actions
Click "New repository secret" for each of the following:

Name	Value
SSH_PRIVATE_KEY	Content of ~/.ssh/ajmain (the private key, not .pub)
VPS_HOST	Your VPS IP (e.g., 20.2.4.20)
VPS_USER	Your SSH username (e.g., root)
PROJECT_PATH	Path to your project on VPS (e.g., /www/wwwroot/test.mirpurianscafe.com)
After this setup, every push to the main branch will automatically deploy your code to your VPS via SSH. ✅
