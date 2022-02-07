---
title: Getting started with Windows Containers
excerpt: Publishing your first Windows Container in Azure
permalink: /blog/2016/08/31/getting-started-with-windows-containers/
categories:
  - .Net
  - Containerization
  - Azure
tags:
  - Cloud
  - Containerization
  - Docker
  - MicroServices
  - Windows Container
---
Soon Windows Server 2016 will be released and so will the Docker Engine compatible Windows Containers feature. Here is a tutorial for you to get started with Windows Containers.

One thing to be aware of when working with containers is that the underlying host must be of the same type of operating system as the container running on the host. Linux containers on Linux hosts and Windows containers on Windows hosts.

First a container host is needed – you can use [Windows 10 Anniversary Update](https://msdn.microsoft.com/en-us/virtualization/windowscontainers/quick_start/quick_start_windows_10) or a Windows Server 2016.

The easiest way of getting started is to spin up a Windows Server 2016 on Azure (get a [free trial](https://azure.microsoft.com/en-us/free/)) with the Container feature enabled.

  1. Select new
  2. Type “container” to filter only for container images
  3. Select the “Windows Server 2016 with Containers” equivalent (as of writing Tech Preview 5)
    ![](/wp-content/uploads/2016/08/CreateWindowsServer2016Container-1024x496.png)
  4. Follow the wizard to specify VM size etc.

Alternatively, you can follow [Windows Containers on Windows Server](https://msdn.microsoft.com/en-us/virtualization/windowscontainers/quick_start/quick_start_windows_server) guide to install the Container feature on an existing Windows Server 2016.

Once you have created the host you can connect to the host via RDP (in the Azure portal use the ”Connect” button in the top menu).

Start up a command prompt or the PowerShell. You can use [PowerShell for Docker](https://github.com/Microsoft/Docker-PowerShell/) or the Docker CLI to execute Docker commands. The commands are the same across platform - no matter if you are using Linux or Windows-based containers. I’ll be using the Docker CLI commands. The common commands are:

* `Docker info` which will show you version etc.
* `Docker images` shows all the images in the local repository
* `Docker ps` show all the running containers
* `Docker ps -a` the -a (or -all) shows all containers including containers that are no longer running
* `Docker run` - run an instance of an image

Let’s get started.

Run the following command to see which images are available in the local repository.

```ps
PS C:\Users\aly\Desktop> docker images
REPOSITORY                  TAG             IMAGE ID     CREATED      SIZE
microsoft/windowsservercore 10.0.14300.1030 02cb7f65d61b 10 weeks ago 7.764 GB
microsoft/windowsservercore latest          02cb7f65d61b 10 weeks ago 7.764 GB
PS C:\Users\aly\Desktop>
```

On my Windows Server 2016 Tech Preview 5 there are two images both with the name `microsoft/windowsservercore` with two different tags. Both of them has the same image id, so they are the same image with two different tags.

To start a container of the image tagged with `latest` run the following:

```ps
PS C:\Users\aly\Desktop> docker run microsoft/windowsservercore:latest
Microsoft Windows [Version 10.0.14300]
(c) 2016 Microsoft Corporation. All rights reserved.

PS C:\Users\aly\Desktop>;
```

The tag is optional, but the default value is `latest`.

The container was started and a command prompt appeared, but then it shut down again and it returned to my PowerShell prompt.

If you want to interact with the container, add the `-it` (interactive) option. You also have the option of specifying which process should be run in the container (cmd is default for this image):

```bash
docker run -it microsoft/windowsservercore:latest cmd
```

Now a command prompt appears and you are in the context of the container. If you modify a file e.g. adds or deletes a file, then the changes will only apply to the container and not the host.

Create a simple file like this:

```bash
echo "Hello Windows Containers" > hello.txt
```

You can exit the container by typing exit, and the container will terminate. Alternatively, you can press CTRL + P + Q to exit and leave the container running.
If you left the container running, you can see the container by listing the Docker processes:

```ps
PS C:\Users\aly\Desktop> docker ps
CONTAINER ID IMAGE                             COMMAND   CREATED       STATUS    PORTS NAMES
23ca16bb6fdb microsoft/windowsservercore:latest "cmd" 4 minutes ago Up 4 minutes pedantic_lamport
```

If the container was terminated, the -a option needs to be appended.
You can reattach to the container by specifying the container id or name. In my case `23ca16bb6fdb` or `pedantic_lamport` like so:

```bash
Docker attach 23ca16bb6fdb
```

You only have the Windows Server Core image in the local repository, but you can download others by pulling from Docker Hub.

```bash
docker pull microsoft/nanoserver
```

Remember that only Windows-based images will run on a Windows host, so if you try the Hello-World Linux-based image, it will fail with a not so elaborate error message.

```ps
PS C:\Users\aly\Desktop> docker pull hello-world
Using default tag: latest
latest: Pulling from library/hello-world
c04b14da8d14: Extracting [==================================================>] 974 B/974 B
failed to register layer: re-exec error: exit status 1: output: ProcessBaseLayer C:\ProgramData\docker\winc266a137b0b1fffedf91d8cd6fcb6560f12afe5277e44bca8cb34ec530286: The system cannot find the path specified.
```

For now it is not easy to differentiate between Linux or Windows-based images on Docker Hub. I would have wished for a filter, making it easier to find relevant images.

Microsoft has a public repository of all the [official released Microsoft container images](https://hub.docker.com/r/microsoft/).
