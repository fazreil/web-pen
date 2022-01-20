# Introduction

This project is created to showcase web testing, especially the front-end of the web. We are going to use NIKTO2 tool to scan a vulnerable website, DVWA (Damn Vulnerable Web Application).

# Running the program

The infrastructure of the project consists of the main DVWA that we are going to perform the test upon. The rest of the infrastructure are mainly going to be set up to accomodate the testing.

## bringing up NIKTO

ref: https://github.com/sullo/nikto/tree/master

Nikto has been added as git submodule. Below are the two steps instructure to check out the submodule. You'll need to bring the submodule code before proceeding to build its docker image.

```
git submodule init
git submodule update
```

### Build nikto image

Get into nikto directory to build nikto

```
cd nikto
docker build . -t nikto
```

You can check the docker image built by listing all docker image

```
docker images
```

## bringing up DVWA

ref: https://hub.docker.com/r/sagikazarmark/dvwa

```
$ docker pull sagikazarmark/dvwa
$ docker run --rm -it -p 8080:80 sagikazarmark/dvwa
```
