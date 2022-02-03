# Customer-hosted Linux

## Pre-requisites & Pre-flight
- [ ] **Server Operating System**: Ubuntu 18
- [ ] **SSH access**
    -  [ ] **Login:** admin username, password, SSH key
    -  [ ] **Firewall access:** VPN or IP whitelisting, if required
-  [ ] **External Access**
    -  [ ] **Port 80**: Let's Encrypt will require HTTP access to setup SSL certificate
    -  [ ] **Port 443**: WAF is accessed via a web browser via HTTPS
-  [ ] **Deployment Files**
    -  [ ] Zuar GitHub deployment repo access or deploy.zip file (download location needed)
-  [ ] **Configuration Settings**
    -  [ ] **Tableau Online or Tableau Server URL** from which vizzes will be displayed
    -  [ ] **WAF URL** e.g. hostname.zuarbase.net

## Deploy WAF

### Transfer Zuar Deployment Scripts

After logging into the target server via [SSH](../common-tasks/ssh.md) as an admin, transfer the Zuar Deployment Scripts to the server:

=== ":fontawesome-brands-github: Git Clone"
    On the server, download files via this command: `git clone https://github.com/zuarbase/zuar-deployment`

=== ":material-file-upload-outline: File Upload"
    If you don't have access to Zuar's git repos, upload the deployment file to the server: 

    1. Obtain `deploy.zip` file from Andy
    2. Use [scp](../common-tasks/ssh.md) to upload file to server

### Install Zip & Unzip Deployment Scripts

Using the package managemr install the `zip` application: `sudo apt install zip unzip`.

Once `zip` is installed, unzip the Deployment Scripts: `unzip deploy.zip`

### Install Docker

Install docker: `bash install_docker.sh`

!!! warning
    Exit the SSH session and reconnect to complete the installation.

Once reconnected to the server, test docker with: `docker run hello-world` (do not run with `sudo`).

Look for the following lines in the output:

``` title="Docker hello world output snippet)"
Hello from Docker! This message shows that your installation appears to be working correctly.
```

### Install Docker Compose

Ater reconnecting to the server, install docker-compose: `bash install_docker_compose.sh`

!!! warning
    Exit the SSH session and reconnect to complete the installation.

Test the docker-compose install: `docker-compose --help`

You should see a help page displayed.

!!! note
    If no help page displays and the connection seems frozen, give it a few minutes. If the help text still hasn't been displayed, hold down ctrl-c until it returns to a prompt (you may have to hold it down for > 1m); wait a bit and run again.

### Point WAF to Tableau

Open the `.env` file (this file is part of the Deployment Scripts) in an editor: `nano .env`

Update the following lines to point to the correct Tableau Server or Tableau Online instance: 

TODO

### Run Deployment

Run deployment script: `bash deploy.sh`

### Start WAF

Once the deployment script has completed, start WAF: `docker-compose up -d`

Check for errors in the docker logs: `docker-compose logs -f`
    - should see only success msgs, no errors, ctrl-c to exit
    - to see what's running: `docker ps`

(add example list of ps output)

### Create SSL certs

Create and apply a Let's Encrypt SSL certificate to allow the site to be reached via https without browser security errors: `docker-compose run --rm zwaf acme <hostname>.zuarbase.net` (replace `<hostname>` with your sub-domain)
    
    - firewall problem: the above cmd failed with: 
        - [Wed Feb  2 21:22:54 UTC 2022] kye-test.zuarbase.net:Verify error:Fetching http://kye-test.zuarbase.net/.well-known/acme-challenge/6fKUASdJlppOsBZ0rY2QxoxY4hVBUgK3Gf5B-V898r8: Timeout during connect (likely firewall problem) [Wed Feb  2 21:22:54 UTC 2022] Please add '--debug' or '--log' to check more details.
        - this is a firewall issue. Andy had to open port to the world. BEFORE running this cmd, check the url on [https://www.isitdownrightnow.com/](https://www.isitdownrightnow.com/)	
    
Restart docker: `docker-compose down && docker-compose up`

## Congrats! :material-party-popper:

The WAF is up and running. Login and let the fun begin!