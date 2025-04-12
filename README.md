# esphome_with_nginx
Docker definitions to create stack that provides ESPHome Device Builder over https with self signed certifcates.

* Assumes you are running some flavour of linux & docker (inc WSL2/Docker Desktop)

Create a directory to hold the config information for the docker stack.

Copy or download the files docker-compose.yml and nginx.conf into the directory.

Inspect the compose file and modify port and directory assignments as required.  
NB by default the ESPHome app will be exposed via http on port 6052, and NGINX will proxy this via https on port 443.
Because 443 is the default port for https, ESPHome will be available via https://localhost or https://<ip address of host>

A subdirectory will be created at run time (called 'config' by default) to hold the configuration and setup for any ESPHome devices you create.  This will persist during container rebuilds.


##Generate Self-Signed certificates:
- from within the config directory
  
mkdir certs
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout certs/selfsigned.key -out certs/selfsigned.crt -subj "/CN=localhost"

then

##To run the stack:

docker-compose up -d
