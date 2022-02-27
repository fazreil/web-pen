title: Internship Presentation
author:
  name: Nor Munira Syafiqa binti Md Zaini
output: deck.html
style: style.css
theme: jdan/cleaver-retro
---
# Introduction

This project is created to showcase web testing, especially the front-end of the web. We are going to use NIKTO2 tool to scan a vulnerable website, DVWA (Damn Vulnerable Web Application).

---

# Running the program

The infrastructure of the project consists of the main DVWA that we are going to perform the test upon. The rest of the infrastructure are mainly going to be set up to accomodate the testing.

---
## bringing up NIKTO

ref: https://github.com/sullo/nikto/tree/master

```
git clone https://github.com/sullo/nikto
# Main script is in program/
cd nikto/program
# Run using the shebang interpreter
./nikto.pl -h http://www.example.com
# Run using perl (if you forget to chmod)
perl nikto.pl -h http://www.example.com
```

---

### Nikto as a git submodule

Nikto has been added as git submodule. Below are the two steps instructure to check out the submodule. You'll need to bring the submodule code before proceeding to build its docker image.

Below is a set of command to initiate and load nikto as a submodule:
```
git submodule init
git submodule update
```

---

#### Understanding git submodule

Git submodule is another git repository residing in the git repository. Although the practise is not recommended due to the design of git to be all inclusive, there are scenarios where including other repositories within cannot be avoided.

---

#### Updating NIKTO source code
Updating NIKTO source code to get the latest from NIKTO's repository
```
git submodule foreach "git fetch origin"
git submodule foreach "git pull origin master"
```

---

# Build nikto image

Get into nikto directory to build nikto
```
cd nikto
docker build -t sullo/nikto .
```

Run nikto docker container from the built image without arguments to display the full help
```
docker run --rm sullo/nikto
```

You can check the docker image built by listing all docker image

```
docker images
```

---

# Bringing up DVWA

ref: https://hub.docker.com/r/sagikazarmark/dvwa

```
$ docker pull sagikazarmark/dvwa
```

---

## DVWA setup

Run the following command to bring up dvwa
```
$ docker run --rm -it -p 8080:80 sagikazarmark/dvwa --name dvwa
```

---

# Testing the DVWA with Nikto
Run the following command to run NIKTO while targetting DVWA. Make sure the ip specified is correct:
```sh
docker run --rm sullo/nikto -h http://172.17.0.2
```

---

## Sample report generated by NIKTO

Below is the sample report generated by NIKTO:
```
docker run --rm sullo/nikto -h http://172.17.0.2 -C all  ✔
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          172.17.0.2
+ Target Hostname:    172.17.0.2
+ Target Port:        80
+ Start Time:         2022-01-23 11:30:27 (GMT0)
---------------------------------------------------------------------------
+ Server: Apache/2.4.10 (Debian)
+ Cookie PHPSESSID created without the httponly flag
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type.
+ Root page / redirects to: login.php
+ Apache/2.4.10 appears to be outdated (current is at least Apache/2.4.46). Apache 2.2.34 is the EOL for the 2.x branch.
+ OSVDB-3268: /config/: Directory indexing found.
+ /config/: Configuration information may be available remotely.
+ OSVDB-3268: /docs/: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ /login.php: Admin login page/section found.
+ 26714 requests: 0 error(s) and 9 item(s) reported on remote host
+ End Time:           2022-01-23 11:31:00 (GMT0) (33 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

---

## Save report generated by NIKTO
To save the report in a specific format, mount /tmp as a volume:
```sh
docker run --rm -v $(pwd):/tmp sullo/nikto -h http://172.17.0.2 -o /tmp/out.json
```

---

# Turning the infrastructure into IAC.
The idea of having the above infrastructure is that they can be provisioned easily by having to execute a piece of code. This practise is called as IAC (Infrastructure as Code). Wittig in his word, "Infrastructure as code (IaC) is the process of managing and provisioning computer data centers through machine-readable definition files, rather than physical hardware configuration or interactive configuration tools.". In IAC, almost everything is built from scratch by using codes. Provisioning tools that is usually being used are usually the kind of tools that leverage on virtualization and their underlying services. Tools such as vagrant, kubernetes (k8s) and docker are popular choices of IAAC tools. Provisioning tools like ansible are popular in provisioning machines across the infrastructure.

ref: Wittig, A., & Wittig, M. (2015). Amazon Web Services in Action (1st ed.). Manning Publications.

---

## Docker compose.
Docker compose leverages on docker container technology. In previous chapters we have brought up multiple services, DVWA and Nikto2 as containers. In order to make that happen, we previously require to run a few commands separately for each of the services. With docker-compose, we could bring the whole infrastructure just by `docker-compose up`. We could also destroy the whole infrastructure just by `docker-compose down`. These kind of flexibility was made by design. The flexibility allow us to bring the whole infrastructure up just to perform required task and bring them down when it is done.

ref: https://docs.docker.com/compose/

---

### Composition of services in docker compose.
In our setup we are going to reproduce the same infrastructure like in previous chapters. There will be two services, DVWA and Nikto2. Services in docker compose are applications available in the said composition.

---

#### using images and build
We are going to use the same method for bringing up DVWA (by fetching latest image from dockerhub) and also Nikto2 (by builing the image before running it as a container).

To view available images in local docker repository, run the following command: `docker images`

---

Sample output when running said command can be seen below:
```sh
$ docker images                                                                                                                                  ✔  6s 
REPOSITORY                                      TAG                    IMAGE ID       CREATED         SIZE
sullo/nikto                                     latest                 936ff09d4fd6   9 days ago      45.4MB
dvja_app                                        latest                 616e3361a56b   6 weeks ago     927MB
mysql                                           5.6                    0befd40a091b   2 months ago    303MB
```

---

#### volume
We need to keep in mind that DVWA would require volume to be attached to store its setting. Volume is like a drive attached to the container. This will require us to define a docker volume to be used with DVWA. The use of docker volume for DVWA will eliminate the need to initialize DVWA over and over again during provisioning.

We can see if there are available docker volumes that we had created by running `docker volume ls`

---

Sample output when running `docker volume ls` is as below:
```sh
$ docker volume ls                                                                                                                                      ✔
DRIVER    VOLUME NAME
local     0ec8a6aa3d6c5edec1598b54e24dc1942c866dc93d9934b637525bd287e275e4
local     1ad1e829810ee613508bea0dd923c6c9463a414d0b5a136eeb5fb01e85f6e853
local     1d64978fc4d2efbfce8391ffd02aff856dc04dc5259a069ad568cb58248ba018
```

---

#### network
Both Nikto2 and DVWA services are going to be run in a dedicated network. We will name the network as `web-pen` network

Similarly, we can see available network in the local docker systems by running: `docker network ls`

Sample output when running `docker network ls` is as below:
```sh
$ docker network ls                                                                                                                                  ✔
NETWORK ID     NAME                          DRIVER    SCOPE
fac9b3baea10   bridge                        bridge    local
70223f4bdb2b   devops-infra_default          bridge    local
d1da3833d288   docker-elk_elk                bridge    local
```

---

### writing docker-compose definition
After looking at the images, network and the services, we are going to build the docker-compose script. The script will define each of the said network and services together which images are going to define the services.

---

#### docker-compose.yaml
A simple docker-compose script is as below:
```yaml
version: "3.9"
services:
  #dvwa
  dvwa:
    image: sagikazarmark/dvwa
    ports:
      - "8080:80"
    networks:
      - web-pen
  #nikto
  nikto:
    build: nikto
    command: "-h http://dvwa"
    depends_on:
      - dvwa
    links:
      - dvwa
    networks:
      - web-pen
networks:
  web-pen:
```

---

The script starts off with the version of docker-compose definition. In this case it is version "3.9".

The second line up until line 19 defines the services. You can see there are two services defined here, dvwa and nikto. They are sharing the same indentation. The last few lines defines the network these services are going to be hosted at. This way each of the services are interacting between each other isolated from any other nodes in our local network.

---

#### dvwa service from docker-compose

Let's take a look at `dvwa` service:
```yaml
  #dvwa
  dvwa:
    image: sagikazarmark/dvwa
    ports:
      - "8080:80"
    networks:
      - web-pen
```

---
The definition of `image: sagikazarmark/dvwa` tells us that we are going to fetch said image from docker.io when bringing up the service.

The next line tells us that we are going to publish local port 8080, while mapping it to `port 80` of the container. This means, we are exposing port 80 of the container out, as `port 8080` on the host machine. We are going to be able to connect to **port 80 (http port)** of dvwa, obviously because dvwa is hosting a website and websites communicates through http protocol. When the service is up, we are going to be able to browse to dvwa through port 8080 of our local host.

Lastly, we define that the service is going to be part of the **web-pen** network

---

#### Nikto2 service from docker-compose

Below is an excerpt of nikto service definition of docker-compose script.

```yaml
#nikto
nikto:
  build: nikto
  command: "-h http://dvwa"
  depends_on:
    - dvwa
  links:
    - dvwa
  networks:
    - web-pen
```

---

The nikto definition is quite similar to `dvwa` service definition. We are going to discuss the part where nikto service definition is different from `dvwa`'s.

Instead of defining docker image, nikto builds the docker image. We have the source code for Nikto, which comes together with its dockerfile. We are able to turn the source code into docker image and use it.

The next line defines `command: "-h http://dvwa"`, which act as an extension of the command being performed by nikto when the container runs. This is the part where we inject the parameter to point to dvwa. The url defined here is `http://dvwa`, `dvwa` refers to the aforementioned `dvwa` service. Although it is possible to point to a specific ip, but the practise is not recommended. It is recommended to point to service name. This way we don't have to find out the ip, which is allocated dynamically. Furthermore, if the service is scaled, it is still possible to point to the service.

The next line says that the nikto service `depends_on: -dvwa`, telling docker to kick off dvwa first or won't start if dvwa is not part of the startup. Though it doesn't mean that nikto will only be brought up after dvwa is brought up.

The other line says `links: -dvwa` which tells docker that this services have a direct connection to dvwa service, enabling us to call dvwa by service name. This is useful in the `command` section above.

---

### Running docker-compose

Running docker-compose script is simple, you can do that with the follwing command:

```
$ docker-compose up
```
This will kick of the creation of network and services defined in docker-compose.yaml.

---

We're good because the whole service is able to be brought up. All we need to do is to run nikto to kick off scanning process. You can kick off nikto by issueing this command in another terminal, just make sure you cd into the docker-compose.yaml directory:

```sh
$ docker-compose run nikto
```

---

Meanwhile, at the other terminal running `docker-compose up`, you can see the output of dvwa being accessed by nikto. Something like below:

```sh
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.191477 2022] [:error] [pid 523] [client 172.30.0.3:55482] script '/var/www/html/wp-license.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.194822 2022] [:error] [pid 523] [client 172.30.0.3:55482] script '/var/www/html/Meuhy.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.246653 2022] [core:error] [pid 614] [client 172.30.0.3:55486] AH00126: Invalid URI in request GET /dana-na/../dana/html5acc/guacamole/../../../../../../etc/passwd?/dana/html5acc/guacamole/ HTTP/1.1

```

---

### Stopping docker-compose
You may stop the entire docker by pressing `ctrl+c` or in a more gracely manner, execute command:
```sh
$ docker-compose down
```