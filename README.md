# How-to-host-a-Minecraft-server-in-Docker-to-play-with-your-friends 🎮
This repository will show the process of hosting a Minecraft server with or without mods using Docker.

Do you have a powerful PC and don't want to spend money hosting Minecraft servers?
In this repository, I'll show you the process for hosting a Minecraft server, along with my experience, mistakes, and potential problems you might encounter along the way.

# Technologies used
- Docker Desktop
- Any IDE (I used VSC)
- A powerful pc

# Step 1: Preparations 🥁
After deciding with your friends which version of Minecraft to play (Java or Bedrock) and the version along with the mods (if you are going to play with mods), it's time to install the necessary programs.

## Install Docker
- Make sure you have Docker installed; install it [here](https://www.docker.com/).

### What is docker and why docker?
Docker is an open-source platform that simplifies the process of building, deploying, and running applications by using containers.
- We're going to set up a Minecraft server in Docker so that when we want to play with our friends, we just have to press the ▶**START**▶ button.

## install your favorite IDE
- Any IDE will work since we're going to create a file called `docker-compose.yaml`
- In my case, I'm using Visual Studio Code because it contains Docker extensions and alerts me if the syntax is incorrect. I installed Visual Studio Code [here](https://code.visualstudio.com/).


# Step 2: Creating the server 🔥
- To create a Minecraft server, according to Mojang's official page on [how to download a Minecraft server](https://www.minecraft.net/es-es/download/server), you must install the `server.jar` file and then run it with the command `java -Xmx1024M -Xms1024M -jar minecraft_server.1.21.10.jar nogui`
- But it will only work while the console is on, and there's no way to connect; the server only starts with the command. Thankfully, **we won't be using this method**.

## Creating Dockerfile
- A Dockerfile is a file used in Docker to create and configure containers. There are many ways to create a Dockerfile; if you want to learn more about how to create a Dockerfile, you can check the [Dockerfile reference](https://docs.docker.com/reference/dockerfile/).
- Make sure your file is named something like `compose.yaml` so it wont work properly
- Throughout my experience setting up the server, my Docker file ended up looking like this.
```
services:
  mc:
    image: itzg/minecraft-server:latest #official docker image for minecraft multiplayer
    container_name: vrhkkk
    tty: true
    stdin_open: true
    ports:
      - "25565:25565" # this doesnt really matter, you can use wichever you want
    environment:
      EULA: "TRUE"
      MEMORY: 20G
      INIT_MEMORY: 20G
      MAX_MEMORY: 20G
      TYPE: Forge
      VERSION: 1.20.1
      
      # Aikars flags (Garbage Collector solver)
      JAVA_OPTS: "-XX:+UseG1GC -XX:MaxGCPauseMillis=200 -XX:+ParallelRefProcEnabled -XX:+UnlockExperimentalVMOptions -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=4 -XX:InitiatingHeapOccupancyPercent=15 -XX:G1MixedGCLiveThresholdPercent=90 -XX:G1RSetUpdatingPauseTimePercent=5 -XX:SurvivorRatio=32 -XX:+PerfDisableSharedMem -XX:MaxTenuringThreshold=1 -Dusing.aikars.flags=https://mcflags.emc.gs -Daikars.new.flags=true -XX:G1NewSizePercent=40 -XX:G1MaxNewSizePercent=50 -XX:G1HeapRegionSize=16M -XX:G1ReservePercent=15"
      
    volumes: # this is were the server will store itself
      - KKKata:/data
    restart: unless-stopped
volumes:
  KKKata:
```
- I'm going to explain the file step by step so you can configure it to your liking, starting with
```
mc:
    image: itzg/minecraft-server:latest #official docker image for minecraft multiplayer
    container_name: vrhkkk
    tty: true
    stdin_open: true
    ports:
      - "25565:25565" # this doesnt really matter, you can use wichever you want
```
- In this code block we can see that I create a container that uses the [itzg/minecraft-server](https://github.com/itzg/docker-minecraft-server?tab=readme-ov-file) image to create a Minecraft server from Docker and be able to initialize it every time I press the start button
- I strongly recommend not touching anything at this step unless you know what you're doing. 🫵
- Next one is
```
environment:
      EULA: "TRUE"
      MEMORY: 20G
      INIT_MEMORY: 20G
      MAX_MEMORY: 20G
      TYPE: Forge
      VERSION: 1.20.1
```
- This is the environment in which the Minecraft server is configured, starting with the initialization variables. In the example, you can see elements like the `EULA`, which must be accepted each time you initialize a Minecraft server. If you want to learn more about these variables, you can consult the [documentation](https://docker-minecraft-server.readthedocs.io/en/latest/variables/#server) for a more detailed configuration.
- The configuration that really matters to us is the memory, the type of Minecraft, and the version. If we look at the code block, we can see that the memory allocated to the server is `20G`, which is different from the initial and maximum memory. These are not necessary to include, but you can assign them to declare to the server what the maximum and minimum will be when the server starts.
- From my personal experience, I can say that for a vanilla server with a maximum of 5 simultaneous players, I recommend 12GB of RAM. However, even with that amount, you might experience some lag due to low TPS. To improve this, you need to change the Aikar flags, which affect the garbage collection process in Minecraft and free up memory. This is an advanced topic for Minecraft servers, and you should research it further if you want to optimize your server, although the best option will always be more RAM.
```
# Aikars flags (Garbage Collector solver)
      JAVA_OPTS: "-XX:+UseG1GC -XX:MaxGCPauseMillis=200 -XX:+ParallelRefProcEnabled -XX:+UnlockExperimentalVMOptions -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=4 -XX:InitiatingHeapOccupancyPercent=15 -XX:G1MixedGCLiveThresholdPercent=90 -XX:G1RSetUpdatingPauseTimePercent=5 -XX:SurvivorRatio=32 -XX:+PerfDisableSharedMem -XX:MaxTenuringThreshold=1 -Dusing.aikars.flags=https://mcflags.emc.gs -Daikars.new.flags=true -XX:G1NewSizePercent=40 -XX:G1MaxNewSizePercent=50 -XX:G1HeapRegionSize=16M -XX:G1ReservePercent=15"
```
- To configure Aikar's flags, I recommend [this page](https://flags.sh/).
- You can copy and paste your flags, mine´s are from a 170 mods server so you can use it freely. **This step is really important so you must do it if you dont want to play with lag.**
```
 volumes: # this is were the server will store itself
      - KKKata:/data
    restart: unless-stopped
volumes:
  KKKata:
```
- KKKata is the name of the volume; you can name it whatever you want. It will be created right when you do `Docker Compose`. Our next step.
# Step 3: Open the server🕺
- To be able to start the server whenever you want, you can mount it in a Docker container, so let's write the first command.
- Open docker.
- Open terminal and search in wich directory your file is, your can travel trough with `cd`.
- Once you are on the samne directory enter the command `Docker compose up -d`.
---
***
---
![McDockerCode](McrandomCode.png)
- Now you will see a lot of commands and code running but dony worry what you actually need to see is the final message.
`[Server thread/INFO] [minecraft/DedicatedServer]: Done (11.656s)! For help, type "help"`
- This effectively means that it is powered on and has successfully started. You can verify on your PC that it is consuming resources and that you have a Minecraft server running on your pc.
- you can connect to your server using your own pc ip address but none of your friends will.
- So we need a public address to let people connecting to our server, this is called a bridge(between your ip and a public one).
- In my case i used playit.gg but you can use wichever you want, playit is a really easy to use, make an account, select minecraft server and install the client.
- then you open playit and the ip that shows will be the one you will use to connect to the server.

  


