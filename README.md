# Introduction

This project is created to showcase web testing, especially the front-end of the web. We are going to use NIKTO2 tool to scan a vulnerable website, DVWA (Damn Vulnerable Web Application).

# Running the program

The infrastructure of the project consists of the main DVWA that we are going to perform the test upon. The rest of the infrastructure are mainly going to be set up to accomodate the testing.

## bringing up NIKTO

ref: https://github.com/sullo/nikto/tree/master

### Nikto as a git submodule

Nikto has been added as git submodule. Below are the two steps instructure to check out the submodule. You'll need to bring the submodule code before proceeding to build its docker image.

Below is a set of command to initiate and load nikto as a submodule:
```
git submodule init
git submodule update
```

#### Understanding git submodule

Git submodule is another git repository residing in the git repository. Although the practise is not recommended due to the design of git to be all inclusive, there are scenarios where including other repositories within cannot be avoided.

#### Updating NIKTO source code
Updating NIKTO source code to get the latest from NIKTO's repository
```
git submodule foreach "git fetch origin"
git submodule foreach "git pull origin master"
```

# Build nikto image

Get into nikto directory to build nikto
```
cd nikto
docker build -t sullo/nikto .
```
Below is the sample output when building NIKTO docker image:
```
$ docker build . -t sullo/nikto     ✔
Sending build context to Docker daemon  2.351MB
Step 1/7 : FROM alpine:3.12
3.12: Pulling from library/alpine
8572bc8fb8a3: Pull complete
Digest: sha256:d9459083f962de6bd980ae6a05be2a4cf670df6a1d898157bceb420342bec280
Status: Downloaded newer image for alpine:3.12
 ---> b0925e081921
Step 2/7 : LABEL version="2.1.6"       author="Author Paul Sec (https://github.com/PaulSec), Nikto User https://github.com/drwetter"       docker_build="docker build -t sullo/nikto:2.1.6 ."       docker_run_basic="docker run --rm sullo/nikto:2.1.6 -h http://www.example.com"       docker_run_advanced="docker run --rm -v $(pwd):/tmp sullo/nikto:2.1.6 -h http://www.example.com -o /tmp/out.json"
 ---> Running in 80699fb05a85
Removing intermediate container 80699fb05a85
 ---> 7e5af2af6c05
Step 3/7 : COPY ["program/", "/nikto"]
 ---> a943454d1c83
Step 4/7 : ENV  PATH=${PATH}:/nikto
 ---> Running in 6d253d24886a
Removing intermediate container 6d253d24886a
 ---> 6f4b24841814
Step 5/7 : RUN echo 'Installing packages for Nikto.'   && apk add --update --no-cache --virtual .build-deps      perl      perl-net-ssleay   && echo 'Creating the nikto group.'   && addgroup nikto   && echo 'Creating the nikto user.'   && adduser -G nikto -g "Nikto user" -s /bin/sh -D nikto   && echo 'Changing the ownership.'   && chown -R nikto.nikto /nikto   && echo 'Finishing image.'
 ---> Running in 16cc3055c305
Installing packages for Nikto.
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/community/x86_64/APKINDEX.tar.gz
(1/4) Installing libbz2 (1.0.8-r1)
(2/4) Installing perl (5.30.3-r0)
(3/4) Installing perl-net-ssleay (1.88-r0)
(4/4) Installing .build-deps (20220123.105113)
Executing busybox-1.31.1-r21.trigger
OK: 44 MiB in 18 packages
Creating the nikto group.
Creating the nikto user.
Changing the ownership.
Finishing image.
Removing intermediate container 16cc3055c305
 ---> 2cb6adf27e06
Step 6/7 : USER nikto
 ---> Running in 561e1b2d8ba7
Removing intermediate container 561e1b2d8ba7
 ---> c5109065b5c4
Step 7/7 : ENTRYPOINT ["nikto.pl"]
 ---> Running in 1ce687609d5e
Removing intermediate container 1ce687609d5e
 ---> 936ff09d4fd6
Successfully built 936ff09d4fd6
Successfully tagged sullo/nikto:latest
```

Run nikto docker container from the built image without arguments to display the full help
```
docker run --rm sullo/nikto
```
Below is the sample output when running the built sullo/nikto docker image:
```
- Nikto v2.1.6
---------------------------------------------------------------------------

   Options:
       -ask+               Whether to ask about submitting updates
                               yes   Ask about each (default)
                               no    Don't ask, don't send
                               auto  Don't ask, just send
       -Cgidirs+           Scan these CGI dirs: "none", "all", or values like "/cgi/ /cgi-a/"
       -config+            Use this config file
       -Display+           Turn on/off display outputs:
                               1     Show redirects
                               2     Show cookies received
                               3     Show all 200/OK responses
                               4     Show URLs which require authentication
                               D     Debug output
                               E     Display all HTTP errors
                               P     Print progress to STDOUT
                               S     Scrub output of IPs and hostnames
                               V     Verbose output
       -dbcheck           Check database and other key files for syntax errors
       -evasion+          Encoding technique:
                               1     Random URI encoding (non-UTF8)
                               2     Directory self-reference (/./)
                               3     Premature URL ending
                               4     Prepend long random string
                               5     Fake parameter
                               6     TAB as request spacer
                               7     Change the case of the URL
                               8     Use Windows directory separator (\)
                               A     Use a carriage return (0x0d) as a request spacer
                               B     Use binary value 0x0b as a request spacer
       -Format+           Save file (-o) format:
                               csv   Comma-separated-value
                               json  JSON Format
                               htm   HTML Format
                               nbe   Nessus NBE format
                               sql   Generic SQL (see docs for schema)
                               txt   Plain text
                               xml   XML Format
                               (if not specified the format will be taken from the file extension passed to -output)
       -Help              This help information
       -host+             Target host/URL
       -404code           Ignore these HTTP codes as negative responses (always). Format is "302,301".
       -404string         Ignore this string in response body content as negative response (always). Can be a regular expression.
       -id+               Host authentication to use, format is id:pass or id:pass:realm
       -key+              Client certificate key file
       -list-plugins      List all available plugins, perform no testing
       -maxtime+          Maximum testing time per host (e.g., 1h, 60m, 3600s)
       -mutate+           Guess additional file names:
       -mutate-options    Provide information for mutates
       -nointeractive     Disables interactive features
       -nolookup          Disables DNS lookups
       -nossl             Disables the use of SSL
       -no404             Disables nikto attempting to guess a 404 page
       -Option            Over-ride an option in nikto.conf, can be issued multiple times
       -output+           Write output to this file ('.' for auto-name)
       -Pause+            Pause between tests (seconds)
       -Plugins+          List of plugins to run (default: ALL)
       -port+             Port to use (default 80)
       -RSAcert+          Client certificate file
       -root+             Prepend root value to all requests, format is /directory
       -Save              Save positive responses to this directory ('.' for auto-name)
       -ssl               Force ssl mode on port
       -Tuning+           Scan tuning:
                               1     Interesting File / Seen in logs
                               2     Misconfiguration / Default File
                               3     Information Disclosure
                               4     Injection (XSS/Script/HTML)
                               5     Remote File Retrieval - Inside Web Root
                               6     Denial of Service
                               7     Remote File Retrieval - Server Wide
                               8     Command Execution / Remote Shell
                               9     SQL Injection
                               0     File Upload
                               a     Authentication Bypass
                               b     Software Identification
                               c     Remote Source Inclusion
                               d     WebService
                               e     Administrative Console
                               x     Reverse Tuning Options (i.e., include all except specified)
       -timeout+          Timeout for requests (default 10 seconds)
       -Userdbs           Load only user databases, not the standard databases
                               all   Disable standard dbs and load only user dbs
                               tests Disable only db_tests and load udb_tests
       -useragent         Over-rides the default useragent
       -until             Run until the specified time or duration
       -update            Update databases and plugins from CIRT.net
       -url+              Target host/URL (alias of -host)
       -useproxy          Use the proxy defined in nikto.conf, or argument http://server:port
       -Version           Print plugin and database versions
       -vhost+            Virtual host (for Host header)
   		+ requires a value

+ ERROR: No host (-host) specified

```

You can check the docker image built by listing all docker image

```
docker images
```

# Bringing up DVWA

ref: https://hub.docker.com/r/sagikazarmark/dvwa

TODO: put a picture of DVWA docker hub page here

```
$ docker pull sagikazarmark/dvwa
```
Sample output when pulling dvwa image:
```
$ docker pull sagikazarmark/dvwa                             ✔
Using default tag: latest
latest: Pulling from sagikazarmark/dvwa
693502eb7dfb: Pull complete
e6c91bb380b4: Pull complete
e111b9773d58: Pull complete
55f12e04cfae: Pull complete
8f1b50e10184: Pull complete
Digest: sha256:1224167ccb59ad64751d52d7beb75fd445a252ae3c13640cfd35c927a2a6725b
Status: Downloaded newer image for sagikazarmark/dvwa:latest
docker.io/sagikazarmark/dvwa:latest
```

## DVWA setup

Run the following command to bring up dvwa
```
$ docker run --rm -it -p 8080:80 sagikazarmark/dvwa --name dvwa
```

Upon running dvwa docker, access the page at port 80. Pay attention to the output of the previous command, the ip can be seen in it.

Another way to find the ip is by running this command in another terminal:
```sh
docker inspect dvwa|grep IPAddress                       ✔
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.2",
                    "IPAddress": "172.17.0.2",
```

login to dvwa with the following username and password:
`admin:admin`

Click on the create database to setup the mysql database built within. Next, try to login to the page with `admin:password` username password pair

TODO: put a picture of DVWA web page here.

# Testing the DVWA with Nikto
Run the following command to run NIKTO while targetting DVWA. Make sure the ip specified is correct:
```sh
docker run --rm sullo/nikto -h http://172.17.0.2
```

## Sample report generated by NIKTO

Below is the sample report generated by NIKTO:
```sh
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

## Save report generated by NIKTO
To save the report in a specific format, mount /tmp as a volume:
```sh
docker run --rm -v $(pwd):/tmp sullo/nikto -h http://172.17.0.2 -o /tmp/out.json
```

---

# Turning the infrastructure into IAC.
The idea of having the above infrastructure is that they can be provisioned easily by having to execute a piece of code. This practise is called as IAC (Infrastructure as Code). Wittig in his word, "Infrastructure as code (IaC) is the process of managing and provisioning computer data centers through machine-readable definition files, rather than physical hardware configuration or interactive configuration tools.". In IAC, almost everything is built from scratch by using codes. Provisioning tools that is usually being used are usually the kind of tools that leverage on virtualization and their underlying services. Tools such as vagrant, kubernetes (k8s) and docker are popular choices of IAAC tools. Provisioning tools like ansible are popular in provisioning machines across the infrastructure.

ref: Wittig, A., & Wittig, M. (2015). Amazon Web Services in Action (1st ed.). Manning Publications.


## Docker compose.
Docker compose leverages on docker container technology. In previous chapters we have brought up multiple services, DVWA and Nikto2 as containers. In order to make that happen, we previously require to run a few commands separately for each of the services. With docker-compose, we could bring the whole infrastructure just by `docker-compose up`. We could also destroy the whole infrastructure just by `docker-compose down`. These kind of flexibility was made by design. The flexibility allow us to bring the whole infrastructure up just to perform required task and bring them down when it is done.

ref: https://docs.docker.com/compose/

### Composition of services in docker compose.
In our setup we are going to reproduce the same infrastructure like in previous chapters. There will be two services, DVWA and Nikto2. Services in docker compose are applications available in the said composition.

#### using images and build
We are going to use the same method for bringing up DVWA (by fetching latest image from dockerhub) and also Nikto2 (by builing the image before running it as a container).

To view available images in local docker repository, run the following command: `docker images`

Sample output when running said command can be seen below:
```sh
$ docker images                                                                                                                                  ✔  6s 
REPOSITORY                                      TAG                    IMAGE ID       CREATED         SIZE
sullo/nikto                                     latest                 936ff09d4fd6   9 days ago      45.4MB
dvja_app                                        latest                 616e3361a56b   6 weeks ago     927MB
mysql                                           5.6                    0befd40a091b   2 months ago    303MB
```

#### volume
We need to keep in mind that DVWA would require volume to be attached to store its setting. Volume is like a drive attached to the container. This will require us to define a docker volume to be used with DVWA. The use of docker volume for DVWA will eliminate the need to initialize DVWA over and over again during provisioning.

We can see if there are available docker volumes that we had created by running `docker volume ls`

Sample output when running `docker volume ls` is as below:
```sh
$ docker volume ls                                                                                                                                      ✔
DRIVER    VOLUME NAME
local     0ec8a6aa3d6c5edec1598b54e24dc1942c866dc93d9934b637525bd287e275e4
local     1ad1e829810ee613508bea0dd923c6c9463a414d0b5a136eeb5fb01e85f6e853
local     1d64978fc4d2efbfce8391ffd02aff856dc04dc5259a069ad568cb58248ba018
```

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

### writing docker-compose definition
After looking at the images, network and the services, we are going to build the docker-compose script. The script will define each of the said network and services together which images are going to define the services.

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

The script starts off with the version of docker-compose definition. In this case it is version "3.9".

The second line up until line 19 defines the services. You can see there are two services defined here, dvwa and nikto. They are sharing the same indentation. The last few lines defines the network these services are going to be hosted at. This way each of the services are interacting between each other isolated from any other nodes in our local network.

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
The definition of `image: sagikazarmark/dvwa` tells us that we are going to fetch said image from docker.io when bringing up the service.

The next line tells us that we are going to publish local port 8080, while mapping it to `port 80` of the container. This means, we are exposing port 80 of the container out, as `port 8080` on the host machine. We are going to be able to connect to **port 80 (http port)** of dvwa, obviously because dvwa is hosting a website and websites communicates through http protocol. When the service is up, we are going to be able to browse to dvwa through port 8080 of our local host.

Lastly, we define that the service is going to be part of the **web-pen** network

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

The nikto definition is quite similar to `dvwa` service definition. We are going to discuss the part where nikto service definition is different from `dvwa`'s.

Instead of defining docker image, nikto builds the docker image. We have the source code for Nikto, which comes together with its dockerfile. We are able to turn the source code into docker image and use it.

The next line defines `command: "-h http://dvwa"`, which act as an extension of the command being performed by nikto when the container runs. This is the part where we inject the parameter to point to dvwa. The url defined here is `http://dvwa`, `dvwa` refers to the aforementioned `dvwa` service. Although it is possible to point to a specific ip, but the practise is not recommended. It is recommended to point to service name. This way we don't have to find out the ip, which is allocated dynamically. Furthermore, if the service is scaled, it is still possible to point to the service.

The next line says that the nikto service `depends_on: -dvwa`, telling docker to kick off dvwa first or won't start if dvwa is not part of the startup. Though it doesn't mean that nikto will only be brought up after dvwa is brought up.

The other line says `links: -dvwa` which tells docker that this services have a direct connection to dvwa service, enabling us to call dvwa by service name. This is useful in the `command` section above.

TODO: draw a picture of network diagram defined by web-pen network.

### Running docker-compose

Running docker-compose script is simple, you can do that with the follwing command:

```
$ docker-compose up
```
This will kick of the creation of network and services defined in docker-compose.yaml.
```sh
Sending build context to Docker daemon  440.9kB
Step 1/7 : FROM alpine:3.12
 ---> b0925e081921
Step 2/7 : LABEL version="2.1.6"       author="Author Paul Sec (https://github.com/PaulSec), Nikto User https://github.com/drwetter"       docker_build="docker build -t sullo/nikto:2.1.6 ."       docker_run_basic="docker run --rm sullo/nikto:2.1.6 -h http://www.example.com"       docker_run_advanced="docker run --rm -v $(pwd):/tmp sullo/nikto:2.1.6 -h http://www.example.com -o /tmp/out.json"
 ---> Using cache
 ---> 7e5af2af6c05
Step 3/7 : COPY ["program/", "/nikto"]
 ---> Using cache
 ---> a943454d1c83
Step 4/7 : ENV  PATH=${PATH}:/nikto
 ---> Using cache
 ---> 6f4b24841814
Step 5/7 : RUN echo 'Installing packages for Nikto.'   && apk add --update --no-cache --virtual .build-deps      perl      perl-net-ssleay   && echo 'Creating the nikto group.'   && addgroup nikto   && echo 'Creating the nikto user.'   && adduser -G nikto -g "Nikto user" -s /bin/sh -D nikto   && echo 'Changing the ownership.'   && chown -R nikto.nikto /nikto   && echo 'Finishing image.'
 ---> Using cache
 ---> 2cb6adf27e06
Step 6/7 : USER nikto
 ---> Using cache
 ---> c5109065b5c4
Step 7/7 : ENTRYPOINT ["nikto.pl"]
 ---> Using cache
 ---> 936ff09d4fd6
Successfully built 936ff09d4fd6
Successfully tagged web-pen_nikto:latest
[+] Running 3/3
 ⠿ Network web-pen_web-pen    Created                                                              0.0s
 ⠿ Container web-pen-dvwa-1   Created                                                              0.1s
 ⠿ Container web-pen-nikto-1  Created                                                              0.1s
Attaching to web-pen-dvwa-1, web-pen-nikto-1
web-pen-nikto-1  | - Nikto v2.1.6
web-pen-nikto-1  | ---------------------------------------------------------------------------
web-pen-nikto-1  | + No web server found on dvwa:80
web-pen-nikto-1  | ---------------------------------------------------------------------------
web-pen-nikto-1  | + 0 host(s) tested
web-pen-nikto-1 exited with code 0
web-pen-dvwa-1   | Starting MySQL database server: mysqld ..
web-pen-dvwa-1   | Checking for tables which need an upgrade, are corrupt or were
web-pen-dvwa-1   | not closed cleanly..
web-pen-dvwa-1   | Starting web server: apache2AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.31.0.2. Set the 'ServerName' directive globally to suppress this message
web-pen-dvwa-1   | .
web-pen-dvwa-1   | tail: unrecognized file system type 0x794c7630 for '/var/log/apache2/access.log'. please report this to bug-coreutils@gnu.org. reverting to polling
web-pen-dvwa-1   | tail: unrecognized file system type 0x794c7630 for '/var/log/apache2/error.log'. please report this to bug-coreutils@gnu.org. reverting to polling
web-pen-dvwa-1   | tail: unrecognized file system type 0x794c7630 for '/var/log/apache2/other_vhosts_access.log'. please report this to bug-coreutils@gnu.org. reverting to polling
web-pen-dvwa-1   | ==> /var/log/apache2/access.log <==
web-pen-dvwa-1   |
web-pen-dvwa-1   | ==> /var/log/apache2/error.log <==
web-pen-dvwa-1   | [Wed Feb 02 14:03:01.398586 2022] [mpm_prefork:notice] [pid 516] AH00163: Apache/2.4.10 (Debian) configured -- resuming normal operations
web-pen-dvwa-1   | [Wed Feb 02 14:03:01.398638 2022] [core:notice] [pid 516] AH00094: Command line: '/usr/sbin/apache2'
web-pen-dvwa-1   |
web-pen-dvwa-1   | ==> /var/log/apache2/other_vhosts_access.log <==
```
At this point, the docker-compose up has finish initializing the services. However there is a line that shows: `web-pen-nikto-1 exited with code 0`, which is not desireable. Nikto has finished initializing and stopped before dvwa is completely up.

We're good because the whole service is able to be brought up. All we need to do is to run nikto to kick off scanning process. You can kick off nikto by issueing this command in another terminal, just make sure you cd into the docker-compose.yaml directory:

```sh
$ docker-compose run nikto
```
Below is sample output of running nikto:
```sh
[+] Running 1/0
 ⠿ Container web-pen-dvwa-1  Running                                                               0.0s
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          172.30.0.2
+ Target Hostname:    dvwa
+ Target Port:        80
+ Start Time:         2022-02-02 12:45:39 (GMT0)
---------------------------------------------------------------------------
+ Server: Apache/2.4.10 (Debian)
+ Cookie PHPSESSID created without the httponly flag
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type.
+ Root page / redirects to: login.php
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Apache/2.4.10 appears to be outdated (current is at least Apache/2.4.46). Apache 2.2.34 is the EOL for the 2.x branch.
+ OSVDB-3268: /config/: Directory indexing found.
+ /config/: Configuration information may be available remotely.
+ OSVDB-3268: /docs/: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ /login.php: Admin login page/section found.
+ 7855 requests: 0 error(s) and 9 item(s) reported on remote host
+ End Time:           2022-02-02 12:45:51 (GMT0) (12 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```
The output should be similar to how you run the scan before.

Meanwhile, at the other terminal running `docker-compose up`, you can see the output of dvwa being accessed by nikto. Something like below:

```sh

web-pen-dvwa-1   | [Wed Feb 02 12:45:51.106940 2022] [:error] [pid 526] [client 172.30.0.3:55480] script '/var/www/html/_adminer.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.111421 2022] [:error] [pid 526] [client 172.30.0.3:55480] script '/var/www/html/ad.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.115088 2022] [:error] [pid 526] [client 172.30.0.3:55480] script '/var/www/html/adminer-4.2.5.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.116111 2022] [:error] [pid 526] [client 172.30.0.3:55480] script '/var/www/html/adminer-4.3.0-en.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.117185 2022] [:error] [pid 526] [client 172.30.0.3:55480] script '/var/www/html/adminer-4.3.0-mysql-en.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.118365 2022] [:error] [pid 526] [client 172.30.0.3:55480] script '/var/www/html/adminer-4.3.0-mysql.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.119625 2022] [:error] [pid 526] [client 172.30.0.3:55480] script '/var/www/html/adminer-4.3.0.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.120886 2022] [:error] [pid 526] [client 172.30.0.3:55480] script '/var/www/html/adminer-4.3.1.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.122156 2022] [:error] [pid 526] [client 172.30.0.3:55480] script '/var/www/html/adminer-4.3.1-en.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.124533 2022] [:error] [pid 526] [client 172.30.0.3:55480] script '/var/www/html/adminer-4.3.1-mysql-en.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.126105 2022] [:error] [pid 526] [client 172.30.0.3:55480] script '/var/www/html/adminer-4.3.1-mysql.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.127229 2022] [:error] [pid 526] [client 172.30.0.3:55480] script '/var/www/html/Adminer.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.191477 2022] [:error] [pid 523] [client 172.30.0.3:55482] script '/var/www/html/wp-license.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.194822 2022] [:error] [pid 523] [client 172.30.0.3:55482] script '/var/www/html/Meuhy.php' not found or unable to stat
web-pen-dvwa-1   | [Wed Feb 02 12:45:51.246653 2022] [core:error] [pid 614] [client 172.30.0.3:55486] AH00126: Invalid URI in request GET /dana-na/../dana/html5acc/guacamole/../../../../../../etc/passwd?/dana/html5acc/guacamole/ HTTP/1.1

```

### Stopping docker-compose
You may stop the entire docker by pressing `ctrl+c` or in a more gracely manner, execute command:
```sh
$ docker-compose down
```
