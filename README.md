# Dockerfile for Android CI
My general usage (very up-to-date) Docker image for Android CI

## Contains:

* Ubuntu 15.10, with wget, curl, zip, python, git, make, gcc (and other build-essential)
* Java8
* Platform tools 23.1
* SDK Tools 25.2.2
* Build Tools 24.0.3
* SDK API 24
* Support Library 24.2.1
* Play Services 9.6.1
* And more (gcm, licensing, billing, apk exp., etc.).
* `/opt/tools/start_emulator.sh` script which can download and start an emulator.


## Common commands
Build image: `docker build -t menny/android:latest .`

Pull from Docker Hub: `docker pull menny/android:latest`

To run image (and attach to STDIN/STDOUT): `docker run -i -t menny/android:latest` 

## General Docker commands:
To stop *all* Docker containers: `docker stop $(docker ps -a -q)`

To remove *all* Docker containers: `docker rm -f $(docker ps -a -q)`

To remove *all* Docker images: `docker rmi -f $(docker images -q)`
