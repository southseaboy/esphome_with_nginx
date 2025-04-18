# esphome_with_nginx

## TL;DR
Make or install the certificates required in ./certs !!  
Run the stack.

## Introduction
ESPHome Device Builder can use the Web Serial API to enable flashing of USB connect ESP devices plugged into the computer running the browser used to access the ESPHome application (n.b. this requires the use of a Chromium based browser - Brave, Chrome and Edge all work).  Web Serial only works when the browser is working in a 'secure context', essentially either session over https://, or an http://localhost... 

This stack is not strictly necessary when ESPHome is exposed via localhost (e.g. on a Windows machine running WSL2/Docker Desktop), but is required when being accessed from another host (or when served from a VM).

This simple docker configuration creates a stack that provides ESPHome Device Builder over https with self signed certifcates.
The instructions are for linux environments where docker and docker-compose are available (works for WSL2/docker desktop).

The only non obvious 'wrinkle' in the config is that NGINX must be setup to handle websockets, which are required for the correct operation of ESPHome (n.b. without websockets ESPHome will fail to execute install/build/compile instructions).

## Basic setup
Clone this repo into a directory to hold the config information for the docker stack.

Inspect the compose file and modify port and directory assignments as required.  
NB by default the ESPHome app will be exposed via http on port 6052, and NGINX will proxy this via https on port 443, but this is mapped to 6050 externally to avoid conflicts with any other web services running on the host.
ESPHome will be available via https://localhost:6050 or https://<ip address of host>:6050

A subdirectory will be created at run time (called 'config' by default) to hold the configuration and setup for any ESPHome devices you create.  This will persist during container rebuilds.


## Generate Self-Signed certificates:
- from within the config directory
  
```  
mkdir certs
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout certs/selfsigned.key -out certs/selfsigned.crt -subj "/CN=localhost"
```
then

## To run the stack:

```
docker compose up -d
```  
NB the -d parameter 'detaches' the output stack from the shell session used to initiate it, without the -d the stack will log any output direct to the terminal, and will stop if the shell session is closed.

## Other useful commands

* `docker compose down`  - shuts down the stack and removes containers and networks, but will not delete volumes and directory/file mounts so e.g. config files will be safe
* `docker ps`  - will list all the running containers on the host, `docker ps -a` - will list all containers (running and stopped)
* `docker restart <containername>` - will restart a particulat container, `stop` and `start` can also be used seperately
* `docker logs <containername>` will display the latest state of the container log file, useful to diagnose issues
