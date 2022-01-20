# Introduction

This project is created to showcase web testing, especially the front-end of the web. We are going to use NIKTO2 tool to scan a vulnerable website, DVWA (Damn Vulnerable Web Application).

# Running the program

## bringing up NIKTO

ref: https://github.com/sullo/nikto/tree/master

Nikto has been added as git submodule.

```
git submodule init
git submodule update
```

## bringing up DVWA

ref: https://hub.docker.com/r/sagikazarmark/dvwa

```
$ docker pull sagikazarmark/dvwa
$ docker run --rm -it -p 8080:80 sagikazarmark/dvwa
```
