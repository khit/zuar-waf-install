# Customer-hosted Windows

## Pre-requisites & Pre-flight
- OS: Ubuntu 18
- SSH access
    - Login username, password, SSH key
    - VPN or IP whitelisting, if required
- External access
    - Port 80: Let's Encrypt will require HTTP access to setup SSL certificate
    - Port 443: WAF is accessed via a web browser via HTTPS
- Deployment files
    - Zuar GitHub deployment repo access or deploy.zip file (download location needed)
- Configuration Settings
    - Tableau Online or Tableau Server URL from which vizzes will be displayed
    - WAF URL e.g. hostname.zuarbase.net

## Deploy WAF

- ssh to server 
- if can't clone repo, upload deploy.zip (got file from Andy)
- Install zip: `sudo apt install zip unzip`
- Unzip: `unzip deploy.zip`
- Install docker: 1.  run `bash install_docker.sh` and follow instructions at the end
- exit ssh, reconnect
- Test docker install: `docker run hello-world` (without sudo)
    - should see:"Hello from Docker! This message shows that your installation appears to be working correctly."
- Install docker-compose: `bash install_docker_compose.sh`
- exit ssh, reconnect
- Test docker-compose install: `docker-compose --help`
    - You should see a help page
    - If you don't and the connection seems frozen, hold down ctrl-c until it returns to a prompt (you may have to hold it down for > 1m); wait a bit and run again
- Complete the info required in .env file to point to the correct tab svr
- run deployment script: `bash deploy.sh`
-  Once script has completed, startup: `docker-compose up -d`
- Check for errors in the docker logs: `docker-compose logs -f`
    - should see only success msgs, no errors, ctrl-c to exit
    - to see what's running: `docker ps`
- Create certs
    - create: `docker-compose run --rm zwaf acme <hostname>.zuarbase.net`
    - firewall problem: the above cmd failed with: 
        - [Wed Feb  2 21:22:54 UTC 2022] kye-test.zuarbase.net:Verify error:Fetching http://kye-test.zuarbase.net/.well-known/acme-challenge/6fKUASdJlppOsBZ0rY2QxoxY4hVBUgK3Gf5B-V898r8: Timeout during connect (likely firewall problem) [Wed Feb  2 21:22:54 UTC 2022] Please add '--debug' or '--log' to check more details.
        - this is a firewall issue. Andy had to open port to the world. BEFORE running this cmd, check the url on [https://www.isitdownrightnow.com/](https://www.isitdownrightnow.com/)	
    - restart docker: `docker-compose down && Ln Ln docker-compose up`