## Overview
* [Prerequisites](#prerequisites)
* [Introduction](#introduction)
* [Setup](#setup)
  * [Cloning the repo](#cloning-the-repo)
  * [Configuring the alarm](#configuring-the-alarm)
  * [Building the docker image](#building-the-docker-image)
  * [Launching PokéAlarm](#launching-pokéalarm)
  * [Stopping PokéAlarm](#stopping-pokéalarm)
* [Integrating with PokémonGo-Map](#integrating-with-pokémongo-map)
  * [Docker network](#docker-network)
  * [Using links](#using-links)
  * [PokémonGo-Map running without docker](#pokémongo-map-running-without-docker)
* [Updating alarms](#updating-alarms)
* [Running multiple alarm containers](#running-multiple-alarm-containers)

## Prerequisites

This guide assumes: 

1) You have [Docker](https://docs.docker.com/) installed and running on your machine

2) You have [Git](https://git-scm.com/downloads) installed and running on your machine

3) You have read and understood the [Alarm Configuration](https://github.com/kvangent/PokeAlarm/wiki/Alarm-Configuration) Wiki

Please familiarize yourself with all of the above before proceeding.

## Introduction

By leveraging [Docker](https://docs.docker.com/), we can run PokéAlarm instances without the need of installing Python, pip or other tools related to it. 

This means that you don't have to go through the hassle of managing python and its multiple versions if you don't feel comfortable doing it, or for any other reason. 

## Setup

### Cloning the repo

The first thing that you need to do is to clone the PokéAlarm repository in the directory you'd like:

```
git clone https://github.com/kvangent/PokeAlarm.git
```

### Configuring the alarm

Now, enter the repository's directory and copy the file called `alarms.json.default` to a file named `alarms.json`. Once that is done, open `alarms.json` and edit it to your liking. Refer to the alarm configuration documentation linked above as needed.

It's also possible, but not required, to edit the configurations file. This will allow you to set default values to be used for most of the alarm's configuration, reducing the number of arguments when launching the instance. To do so, make a copy of the `config.ini.example` file located inside the config dir and rename it to `config.ini`. Now, just uncomment the desired lines and provide the values.

### Building the docker image

Once alarms.json is edited, we've got to build the docker image that we will use to launch our PokéAlarm container. To do so, execute the following command from the repository's root directory:

```
docker build -t pokealarm .
```

This will create an image with the tag pokealarm, the dot indicates it should use the contents from the directory it was executed.

### Launching PokéAlarm

With the docker image created, we can launch a PokéAlarm container. To do so is as simple as executing:

```
docker run --name alarm -d pokealarm -k YOUR_GMAPS_API_KEY -H 0.0.0.0
```

This will launch a docker container named `alarm` using the image we just created and tagged `pokealarm`

As you can see, we added the `-H` flag and set its value to `0.0.0.0`, this is necessary when running PokéAlarm as docker containers because otherwise it would listen only to calls made from inside the host container.

### Stopping PokéAlarm

Simply execute `docker stop alarm` and `docker rm alarm`. The removal of the container is a necessary step because we want to reuse its name.

## Integrating with PokémonGo-Map

We will cover 3 scenarios for integration with your PokémonGo-Map. For the first two scenarios, we will assume that given your interest in PokéAlarm with docker, you are already running [PokémonGo-Map on docker containers](https://pgm.readthedocs.io/en/develop/advanced-install/docker.html). For the last, we will assume you have it running locally with python.

### Docker network

If you have followed the PokémonGo-Map documentation for docker, you probably have a docker network already setup. All you would need to do is to add the network's name to you docker run command that was used before, like this:

```
docker run --name alarm --net=NETWORK_NAME -d pokealarm -k YOUR_GMAPS_API_KEY -H 0.0.0.0
```

Once that's done, you are able to reach your PokéAlarm container from any other container in the same network, using the container's name as host value. For example, you could add `-wh 'http://alarm:4000'` to your PokémonGo-Map instances.

### Using links

If you are not running a docker network what you need to do is to link your PokémonGo-Map container to your PokéAlarm container. To do so, launch you PokéAlarm container as we've done before, but this time launch tou PokémonGo-Map container with the added flag `--link alarm` before the image's name on your docker run command. With that link declared, you can add `-wh 'http://alarm:4000'` to the end of the docker run command and it will be able to find your PokéAlarm container.

### PokémonGo-Map running without docker

If you are not running PokémonGo-Map on docker, what we need to do for it to be reachable is to bind a port on your host to the container's port. This is easily done by adding the `-p` docker flag to your docker run command:

```
docker run --name alarm -d -p 4000:4000 pokealarm -k YOUR_GMAPS_API_KEY -H 0.0.0.0
```

Once you execute that, you will be able to reach your PokéAlarm container on port `4000` by port `4000` on your localhost. This means you could add `-wh 'http://127.0.0.1:4000'` to your `./runserver.py` command and it would be able to post to the webhook.

## Updating alarms

If you want to update the `alarms.json` or `config.ini` files or wish to create any other alarm configuration file, you are free to do so at any time. However, you will need to rebuild your docker image afterwards, so that it can copy the new/edited files into the image.

## Running multiple alarm containers

All you have to do is to execute the appropriate (described above) docker run command multiple times, giving each container a new name. For example, this is how we could do that using the docker network approach described before: 

```
docker run --name commons --net=NETWORK_NAME -d pokealarm -k YOUR_GMAPS_API_KEY -H 0.0.0.0
docker run --name rares --net=NETWORK_NAME -d pokealarm -k YOUR_GMAPS_API_KEY -H 0.0.0.0 -c rares.json
docker run --name ultra --net=NETWORK_NAME -d pokealarm -k YOUR_GMAPS_API_KEY -H 0.0.0.0 -c ultra-rares.json
```

In the above block we are launching 3 containers of the `pokealarm` image in a docker network, all of which could be accessed by other containers in the network by their names.

* The first is named `commons` and using the default configuration `alarms.json`
* The second is named `rares` and uses the configuration file named `rares.json`
* The third is named `ultra` and used the configuration file named `ultra-rares.json`

If running PokémonGo-Map without docker, you would simply have to add different port bindings to each container, like: 

```
docker run --name commons -p 4000:4000 -d pokealarm -k YOUR_GMAPS_API_KEY -H 0.0.0.0
docker run --name rares -p 4001:4000 -d pokealarm -k YOUR_GMAPS_API_KEY -H 0.0.0.0 -c rares.json
docker run --name ultra -p 4002:4000 -d pokealarm -k YOUR_GMAPS_API_KEY -H 0.0.0.0 -c ultra-rares.json
```

Meaning that the containers would be reachable on `http://localhost:4000`, `http://localhost:4001` and `http://localhost:4002` respectively.