1. Resource Group
Resource Group Name: cms
2. SQL Database
DB name: cms
Server: cms.database.windows.net
DB region: us-west_europe
Admin login: cmsadmin
Admin password: Project5
Resource group: cms
DB workload env: Development
DB compute + storage: DTU - Basic
Press the "Next: Networking" button, then select "Public Endpoint", and set both of the Firewall rules that appear to "Yes".
Set everything else to default
Run SQL queries in sql_scripts/ directory after completion, starting from the users table. Don't forget to take screenshots.
3. Storage Account
Resource group: cms
Storage account name: imagesproject5 (needs to be unique)
Advanced - Allow enabling anonymous access on individual containers: Enable
Advanced - Access tier: Cool
Network access: Enable public access from all networks (the default)
Create container named "images". Set its access level to Container.
From Security + networking > Access keys:
Blob Storage key: gIkdJdpby6ZEvRmD7t2vY1zfb6WndwBLPWh9d4m9GoVHsrN4ptrgHsrpRKi2sOFL4zMVEoh7lTIz+ASttnW6Eg==
4. Microsoft Entra ID
4.1. App Registration
Name: cmsEntraID
Who can use? "Accounts in any organizational directory (Any Microsoft Entra ID tenant - Multitenant) and personal Microsoft accounts (e.g. Skype, Xbox)"
4.2. Secret Creation
Secret description: cmsSecret
Secret Key: dee81440-4368-46e3-8b58-6be7269734b5
Client Secret: ZVu8Q~d_tlD.wY5dTwvek7WQOAgyyaxX~gp3wa7b
Application (client) ID: 3f455150-88d3-4570-ab42-1ef078080ae9
5. Application
Pick either of the following two options for setting up your application. Once you have set up the application successfully, you may begin to work on the OAuth2 login feature.

5.1. OPTION 1: Virtual Machine
Name: vm-cms
Authentication type: Password
Username: cmsUser
Password: cmsP4ssw0rd111
Size: Standard B1ls (the cheapest)
Ubuntu 24.04
Select inbound ports: Choose 80, 443, and 22
Step 1. Connect to VM via SSH

Step 2. Setup GitHub SSH key

Run the following commands in the terminal:

ssh-keygen -t ed25519 -C "yourgithubemail@example.com"
Press enter on the next few prompts
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
Copy the output to https://github.com/settings/keys(opens in a new tab)
Step 3. Setup the website.

Setup HTTPS:

sudo mkdir -p /etc/nginx/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt
Any values are accepted for the next questions. Here is an example:
Country Name (2 letter code) [AU]:US
State or Province Name (full name) [Some-State]:California
Locality Name (eg, city) []:Palo Alto
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:
Email Address []:hello@example.com
Then, run the following commands:

sudo curl https://packages.microsoft.com/config/ubuntu/22.04/prod.list | sudo tee /etc/apt/sources.list.d/mssql-release.list
sudo apt update
git clone [git@github.com:user/repo.git] web
sudo apt -y update && sudo apt-get -y install nginx python3-venv unixodbc unixodbc-dev
sudo ACCEPT_EULA=Y apt-get install -y msodbcsql17
sudo unlink /etc/nginx/sites-enabled/default
sudo vim /etc/nginx/sites-available/reverse-proxy.conf
Paste this code into reverse-proxy.conf:
server {
  listen 80;
  location / {
    proxy_pass https://localhost:5555;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection keep-alive;
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }
}
server {
  listen 443 ssl;
  ssl_certificate /etc/nginx/ssl/nginx.crt;
  ssl_certificate_key /etc/nginx/ssl/nginx.key;
  location / {
    proxy_pass https://localhost:5555;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection keep-alive;
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }
}
sudo ln -s /etc/nginx/sites-available/reverse-proxy.conf /etc/nginx/sites-enabled/reverse-proxy.conf
sudo service nginx restart
cd ~/web
python3 -m venv venv
. venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
Copy-paste the code from credentials.sh to the terminal
python application.py
5.2. OPTION 2: Web App (easier)
Name: udacitycms.azurewebsites.net
Runtime stack: Python 3.10
Pricing Plan: Free F1
If you are getting a "Validation failed for a resource" error, pick a different region.
After creation:

Settings -> Environment variables - Add the following variables (sample values are included, replace them with your values):
BLOB_ACCOUNT: imagesproject5
BLOB_CONTAINER: images
BLOB_STORAGE_KEY: gIkdJdpby6ZEvRmD7t2vY1zfb6WndwBLPWh9d4m9GoVHsrN4ptrgHsrpRKi2sOFL4zMVEoh7lTIz+ASttnW6Eg==
BLOB_CONNECTION_STRING: DefaultEndpointsProtocol=https;AccountName=imagesproject5;AccountKey=gIkdJdpby6ZEvRmD7t2vY1zfb6WndwBLPWh9d4m9GoVHsrN4ptrgHsrpRKi2sOFL4zMVEoh7lTIz+ASttnW6Eg==;EndpointSuffix=core.windows.net
SQL_SERVER: cmsproject5.database.windows.net
SQL_DATABASE: cms (cmsproject5/cms)
SQL_USER_NAME: cmsadmin
SQL_PASSWORD: Project5
CLIENT_SECRET: ZVu8Q~d_tlD.wY5dTwvek7WQOAgyyaxX~gp3wa7b
SECRET_KEY: dee81440-4368-46e3-8b58-6be7269734b5
CLIENT_ID: 3f455150-88d3-4570-ab42-1ef078080ae9


Deployment Center
Source: GitHub
Pick the repo that contains the starter files.
6. Setting up OAuth2
At this point, your application should already be running. You should already be able to log in with username admin and password pass and you can create new posts or update existing ones.

The next part is getting the OAuth2 login to work.

Go to Microsoft Entra ID > App Registrations, click on the App Registration created earlier, and then pick Authentication from the left sidebar.

6.1. Microsoft Entra ID - Authentication - Add a Platform - Web
Redirect URIs: https://[IP ADDRESS FROM VM or WEB APP ADDRESS]/getAToken
logout URL: https://[IP ADDRESS FROM VM or WEB APP ADDRESS]/login
