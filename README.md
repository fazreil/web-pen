# Introduction

# Running the program

## bringing up NIKTO

ref: https://github.com/sullo/nikto/tree/master

Nikto has been added as git submodule.

```
git submodule init
```

## bringing up DVWA

ref: https://hub.docker.com/r/sagikazarmark/dvwa

```
$ docker pull sagikazarmark/dvwa
$ docker run --rm -it -p 8080:80 sagikazarmark/dvwa
```
