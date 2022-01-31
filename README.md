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
```
docker inspect dvwa|grep IPAddress                       ✔
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.2",
                    "IPAddress": "172.17.0.2",
```

login to dvwa with the following username and password:
`admin:admin`

Click on the create database to setup the mysql database built within. Next, try to login to the page with `admin:password` username password pair

# Testing the DVWA with Nikto
Run the following command to run NIKTO while targetting DVWA. Make sure the ip specified is correct:
```
docker run --rm sullo/nikto -h http://172.17.0.2
```

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

## Save report generated by NIKTO
To save the report in a specific format, mount /tmp as a volume:
```
docker run --rm -v $(pwd):/tmp sullo/nikto -h http://dvwa -o /tmp/out.json
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

#### volume
We need to keep in mind that DVWA would require volume to be attached to store its setting. Volume is like a drive attached to the container. This will require us to define a docker volume to be used with DVWA. The use of docker volume for DVWA will eliminate the need to initialize DVWA over and over again during provisioning.

#### network
Both Nikto2 and DVWA services are going to be run in a dedicated network. We will name the network as `web-pen` network
