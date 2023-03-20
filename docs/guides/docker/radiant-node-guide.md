# Run a Docker Radiant Node

## Installing Docker:
The process for installing docker engine depends on your operating system. 
Refer to https://docs.docker.com/engine/install/ and look up instructions for your OS.

## Pulling Docker image:
You can browse https://hub.docker.com/ for images that you might find use for. At the time of writing there are two options for running a Radiant node: just the node and the node with electrum.

The image for just the node: https://hub.docker.com/r/radiantcommunity/radiant-node.

The image for the node + electrum: https://hub.docker.com/r/radiantcommunity/electrumx_radiant_node.

If you want to run the electrum version there is no need to also run the pure node container.


Under Docker commands you can see a command to fetch the image so that you store it locally. 
If you know what image you want there is no need to do this manually. 
When we run the image and Docker can't find it locally it will search Docker Hub for it.

## Running the container:
The command for starting the node container is:

```
sudo docker run --name radiantnode -itd radiantcommunity/radiant-node
```

![](https://github.com/Bactol-git/Radiant-node/blob/main/img/run.png)

You might not need to sudo if you have given docker sudo rights. 
The -itd tag is to run the container interactively, with tty (terminal) and detatched (background). 
The last bit is the reference to the image you want to run.

The name tag is optional, but makes it easier when we specify it when we want to inspect the container and when we stop it.

If it is the first time you run the command and dont have the image locally you will see the download progress. This is only for when you fetch new images. The second time you run it it will be much faster as you already have the image. When you run the command you should see a hash, and that means the container started.

The command for starting the node + electrum container is:

```
sudo docker run --name radiantelectrum -itd radiantcommunity/electrumx_radiant_node
```

## Inspecting the container:
You can run this to see what containers are currently running:

```
sudo docker ps
```

![](https://github.com/Bactol-git/Radiant-node/blob/main/img/ps.png)

and you can run this to see all containers that has been ran:

```
sudo docker ps -a
```

![](https://github.com/Bactol-git/Radiant-node/blob/main/img/ps-a.png)

Acces the container to inspect and troubleshoot:
We can access the terminal of the running container to inspect and troubleshoot it using this command:

```
sudo docker exec -it radiantnode sh
```

This will open up the terminal in the container and we can run commands to see that everything runs as expected. You will see a # symbol meaning we are in the terminal of the node. Now we can run: 

```
radiant-cli -getinfo 
```

to see that our node is running.
You can see other useful commands to run on the node in the radiant node guide (https://radiant4people.com/guides/node/compile/#useful-commands).

![](https://github.com/Bactol-git/Radiant-node/blob/main/img/radiant-cli.png)

To go back out of the container simply use run``` exit ``` inside the container.

We now have a functional radiant node up and running, but why stop there? 
We can easilly run multiple nodes at once. Just start another node using the same image (but different name). 
This will spawn a new container with a new node running. For example:

```
sudo docker run --name radiantnode2 -itd radiantcommunity/radiant-node
```

We can now use

```
sudo docker ps
```

to see that we have two nodes running.

## Stopping container:
When its time to stop the container we simply run: 

```
sudo docker stop radiantnode
```

"radiantnode" is the name we specified when we started the first container. To stop the second container we started:

```
sudo docker stop radiantnode2
```

To stop the node + electrum:

```
sudo docker stop radiantelectrum
```
