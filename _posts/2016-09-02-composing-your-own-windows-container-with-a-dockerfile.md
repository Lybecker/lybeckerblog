---

title: Composing your own Windows Container with a Dockerfile
date: 2016-09-02T08:48:45+01:00


guid: http://www.lybecker.com/blog/?p=1396
permalink: /2016/09/02/composing-your-own-windows-container-with-a-dockerfile/
dsq_thread_id:
  - "5113471734"
categories:
  - .Net
  - Cloud
  - Containerization
tags:
  - Cloud
  - Containerization
  - Docker
  - MicroServices
  - Windows Container
---
In the previous blog post [Getting Started with Windows Containers](/blog/2016/08/31/getting-started-with-windows-containers/) I showed how to run containers and pull existing containers from the public repository [Docker Hub](https://hub.docker.com/). Now it is time to create a new image and add it to Docker Hub.

I wanted to create a Hello World image that works on Windows Containers. I chose to create the Hello-World program using .NET Core – it runs cross platform (Linux, Mac & Windows), but I’m using Windows.

On the Windows Server 2016 container host I created in the previous blog post, I needed to install .NET Core. You can either go to <http://dot.net/core> to download or execute the following PowerShell to download the installer.

<pre class="brush: powershell; gutter: false; title: ; notranslate" title="">Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -OutFile c:\dotnetinstall.exe
</pre>

I created a folder called HelloWorld and executed the following commands in a command prompt:

<pre class="brush: plain; gutter: false; highlight: [1,3,8,19]; title: ; notranslate" title="">PS C:\Users\aly\Desktop\HelloWorld&gt; dotnet new
Created new C# project in C:\Users\aly\Desktop\HelloWorld.
PS C:\Users\aly\Desktop\HelloWorld&gt; dotnet restore
log : Restoring packages for C:\Users\aly\Desktop\HelloWorld\project.json...
log : Writing lock file to disk. Path: C:\Users\aly\Desktop\HelloWorld\project.lock.json
log : C:\Users\aly\Desktop\HelloWorld\project.json
log : Restore completed in 1049ms.
PS C:\Users\aly\Desktop\HelloWorld&gt; dotnet run
Project HelloWorld (.NETCoreApp,Version=v1.0) will be compiled because expected outputs are missing
Compiling HelloWorld for .NETCoreApp,Version=v1.0

Compilation succeeded.
0 Warning(s)
0 Error(s)

Time elapsed 00:00:01.8161040

Hello World!
PS C:\Users\aly\Desktop\HelloWorld&gt; dotnet publish
Publishing HelloWorld for .NETCoreApp,Version=v1.0
Project HelloWorld (.NETCoreApp,Version=v1.0) was previously compiled. Skipping compilation.
publish: Published to C:\Users\aly\Desktop\HelloWorld\bin\Debug\netcoreapp1.0\publish
Published 1/1 projects successfully
PS C:\Users\aly\Desktop\HelloWorld&gt;
</pre>

You can use any executable, I just needed something simple to work with. .NET Core is my simple choice.
I am going to base my image on the microsoft/windowsservercore image. But instead of installing .NET Core myself, I can use an image that already has .NET Core installed called microsoft/dotnet:windowsservercore. See all the official Microsoft Docker images at the [Microsoft Docker Hub repository](https://hub.docker.com/r/microsoft/). So pull down the microsoft/dotnet:windowsservercore image

<pre class="brush: plain; gutter: false; title: ; notranslate" title="">docker pull microsoft/dotnet:windowsservercore
</pre>

If you want to play with it run the following command to start a container and a command prompt will appear. Type exit when you want to go back to the container host.

<pre class="brush: plain; gutter: false; title: ; notranslate" title="">docker run -it microsoft/dotnet:windowsservercore
</pre>

Now let’s create the Hello World image. Create a file called Dockerfile without extension outside the /HelloWorld folder and add this:

<pre class="brush: plain; title: ; notranslate" title="">FROM microsoft/dotnet:windowsservercore
MAINTAINER Anders Lybecker (@Lybecker)
ADD /helloworld/bin/Debug/netcoreapp1.0/publish/ c:\\code
ENTRYPOINT dotnet c:\\code\\helloworld.dll
</pre>

Line 1 dictates that I’m basing the Hello World image on the official image microsoft/dotnet:windowsservercore
Line 2 is just maintainer information and is optional
Line 3 adds the content of the publish folder of the container host to the image in c:\code
Line 4 sets the default command to execute the Hello World program

For more details see the [Dockerfile reference](https://docs.docker.com/engine/reference/builder/).

Build the container by executing the docker build command. The image is named myhelloword and tagged with v1 for version 1. The last . specifies where the Dockerfile is located.

<pre class="brush: plain; gutter: false; highlight: [1]; title: ; notranslate" title="">PS C:\Users\aly\Desktop&gt; docker build -t myhelloworld:v1 .
Sending build context to Docker daemon 341.5 kB
Step 1 : FROM microsoft/dotnet:windowsservercore
---&gt; 1e21a0790e96
Step 2 : MAINTAINER Anders Lybecker (@Lybecker)
---&gt; Running in 6812a0ecf67d
---&gt; 5dce994e32a4
Removing intermediate container 6812a0ecf67d
Step 3 : ADD /helloworld/bin/Debug/netcoreapp1.0/publish/ c:\\code
---&gt; 22fade758f23
Removing intermediate container 665f9e677611
Step 4 : ENTRYPOINT dotnet c:\\code\\helloworld.dll
---&gt; Running in dad461888c4c
---&gt; 204ed6ed0b59
Removing intermediate container dad461888c4c
Successfully built 204ed6ed0b59
</pre>

That’s it. You have now created your own image. Let’s try to run it

<pre class="brush: plain; gutter: false; title: ; notranslate" title="">PS C:\Users\aly\Desktop&gt; docker run myhelloworld:v1
Hello World!
</pre>

You can see the image in the local image repository with the docker images command.

<pre class="brush: plain; gutter: false; title: ; notranslate" title="">PS C:\Users\aly\Desktop&gt; docker images
REPOSITORY                  TAG               IMAGE ID     CREATED        SIZE
myhelloworld                v1                204ed6ed0b59 10 minutes ago 8.111 GB
microsoft/dotnet            windowsservercore 1e21a0790e96 2 weeks ago    8.111 GB
microsoft/windowsservercore 10.0.14300.1030   02cb7f65d61b 10 weeks ago   7.764 GB
microsoft/windowsservercore latest            02cb7f65d61b 10 weeks ago   7.764 GB
</pre>

The myhelloworld:v1 image is based on the microsoft/dotnet:windowsservercore which is based om microsoft/windowsservercore:10.0.14300.1030. You can see all the layers via the docker history command

<pre class="brush: plain; gutter: false; highlight: [1]; title: ; notranslate" title="">PS C:\Users\aly\Desktop&gt; docker history myhelloworld:v1
IMAGE CREATED CREATED BY SIZE COMMENT
204ed6ed0b59 12 minutes ago cmd /S /C #(nop) ENTRYPOINT ["cmd" "/S" "/C" 46.58 kB
22fade758f23 12 minutes ago cmd /S /C #(nop) ADD dir:4bef0fa9bcfacdaa9bb8 40.96 kB
5dce994e32a4 12 minutes ago cmd /S /C #(nop) MAINTAINER Anders Lybecker 181.2 MB
1e21a0790e96 2 weeks ago cmd /S /C mkdir warmup &amp;&amp; cd warmup &amp; 40.96 kB
2 weeks ago cmd /S /C #(nop) ENV NUGET_XMLDOC_MODE=skip 4.756 MB
2 weeks ago cmd /S /C setx /M PATH "%PATH%;%ProgramFiles% 160.7 MB
2 weeks ago cmd /S /C powershell -NoProfile -Command 40.96 kB
2 weeks ago cmd /S /C #(nop) ENV DOTNET_SDK_DOWNLOAD_URL 40.96 kB
2 weeks ago cmd /S /C #(nop) ENV DOTNET_SDK_VERSION=1.0. 7.764 GB
</pre>

I have made my Hello World image available on [Docker Hub](https://hub.docker.com/r/anderslybecker/), so you can pull and run the image on your Windows Container host like so:

<pre class="brush: plain; gutter: false; highlight: [1,5]; title: ; notranslate" title="">PS C:\Users\aly\Desktop&gt; docker pull anderslybecker/dotnet-hello-world:windowsservercore
windowsservercore: Pulling from anderslybecker/dotnet-hello-world
Digest: sha256:1df17a8a38d969b71b38333be25f76757e69f1537c7a86a6ee966bca87163464
Status: Image is up to date for anderslybecker/dotnet-hello-world:windowsservercore
PS C:\Users\aly\Desktop&gt; docker run anderslybecker/dotnet-hello-world:windowsservercore
Hello World!
</pre>
