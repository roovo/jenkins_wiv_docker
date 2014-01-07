# Jenkins Wiv Docker

Basically a Vagrant setup for building a Jenkins instance that can work with
docker containers.

## credit
Is due to the peeps @cambridge-healthcare - this (and the dockerize project
it uses) are based most heavily on their stuff.  The jenkins setup used here
has been extracted from their [hi_sinatra-docker project]
(https://github.com/cambridge-healthcare/hi_sinatra-docker).

The main difference here is that this uses my version of dockerize and it
doesn't include any example project to use for building - so it's Jenkins
with docker only.

## jenkins setup

Assuming you know how to use Vagrant and have built your VM, the jecnkins
instance should be accessible on your host at http://localhost:8080.

* Create a new build for the project (a free-style project)
* Enter the following as the build shell script:

```sh
/bin/bash -c "source $HOME/.profile && dockerize test https://github.com/roovo/sinatra_docker_test && dockerize boot https://github.com/roovo/sinatra_docker_test"
```

[assumes you want to build https://github.com/roovo/sinatra_docker_test]

This first runs the tests, then boots the container if the tests pass.  This
requires a container compatible with dockerize: i.e. running the container will
run the app and running the container with a `--run-tests` command with run
the tests.

If you run the build and it passes, you should be left with a running container
named with the project name, branch name and git sha1 (e.g.
`sinatra_docker_test:master.193df41`).

If the build fails you will be left with a non-runnning container (similarly
named).

An image is created for eash build irrespective of whether the build passes
or fails.
