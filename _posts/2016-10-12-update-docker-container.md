---
layout: post
title: "Update Docker container"
description: "automatic script for checking if container needs to be recreated from newer docker image"
category: lessons
tags: [tutorial, docker]
---
{% include JB/setup %}

This little script automatically creates a new container from a newer image (if any). 
Just fill in the organization/user and the image name.

{% highlight bash %}
#!/usr/bin/env bash
set -e
REGISTRY="wischlabs"
BASE_IMAGE="ofm_helper"
IMAGE="$REGISTRY/$BASE_IMAGE"
CID=$(docker ps | grep $BASE_IMAGE | awk '{print $1}')
docker pull $IMAGE

for im in $CID
do
    LATEST=`docker inspect --format "{{.Id}}" $IMAGE`
    RUNNING=`docker inspect --format "{{.Image}}" $im`
    NAME=`docker inspect --format '{{.Name}}' $im | sed "s/\///g"`
    echo "Latest:" $LATEST
    echo "Running:" $RUNNING
    if [ "$RUNNING" != "$LATEST" ];then
        echo "upgrading $NAME"
        docker stop $NAME
        docker rm -f $NAME
        docker run -d --name $BASE_IMAGE --restart=unless-stopped $IMAGE
    else
        echo "$NAME up to date"
    fi
done
{% endhighlight %}

You might also want to add a flag into the docker run command to mount other data-containers into your new container. You can specify this like this:
{% highlight bash %}
docker run -d --name $BASE_IMAGE --volumes-from data_container --restart=unless-stopped $IMAGE
{% endhighlight %}
