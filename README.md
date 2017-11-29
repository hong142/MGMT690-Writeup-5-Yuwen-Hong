# Containerizing analyses
Last week, we designed our full security image object detection pipeline, which includes stages of preprocessing, object detection and postprocessing. This writeup discusses the software container, the last piece we need before we deploy the whole pipeline. Specifically, we are going to briefly introduce Docker, the most popular software to facilitate container creation for data analytics. We will containerize one of the stages, validation, to show you how Docker works, so that you can deploy similar things.
## Why software containers?
When we have an application build, we want to take this application off laptop, and get them running on the cloud or some instance in the cloud. For our case, it would be Python application and ec2 of amazon. To achieve this, typically we have four options. 
### One app per instance
The most straightforward way is to install one application per instance, and install its dependencies directly on that instance. That means every time you want to run an application on a certain machine, you will need to manually create an instance and copy over code and dependencies. The process is inefficient in terms of both time and space.
### Multiple apps per instance
Likewise, you can install multiple applications together with their dependencies directly on one instance. However, in addition to the issue of inefficiency, this approach also raises issues of duplication or even conflict because not all the dependencies you want to run on the same machine coexist nicely. For example, if you have two applications, both running scikit-learn, then typically you will install scikit-learn twice. What's worse, if two applications are running different versions of scikit-learn, then you may encounter collisions.
### Virtual Machine (VM) 
To overcome the problems of having to manually figure out which application can run on which cloud instance, we get the third option, virtual machine(VM), and it can be one or many per instance. VM partition all the things we need for multiple applications, and run those things on a virtual environment with disk space allocated. 

Though VM simplifies the process to allocate applications across instances, it doesn’t really solve the issue of resource optimization. Essentially, you still have to move thigs around all the time and constantly update them, between machines or on a new machine. Consider the amount of data to transfer, the process would be slow in general. Also, most of the time, you are probably not using that much RAM or the space as you assigned to an application, but nothing else can use that surplus space. As a result, number of things you can run on a certain machine is strictly limited.
### Containers
Inconvenience associated with VM brings us to the forth option - software containers. Docker is a widely adopted user-friendly interface to containers that provides certain implementation of software containers. Unlike a VM, which is an entire operation system along with dependencies and applications, a container is a small package that includes your application, its dependencies and the files systems the application can operate on. In the docker, all those packages run on the same underlying docker engine, which interfaces with the underling operating system and manages resources. All packages share the underlying kernel, including memory, disc usage and CPU. If one package doesn’t use up the memory, then all the rest is free for others to use.

Moreover, we are running an analytics project and we expect the application to make decision by itself, such as make detection and inform customers with threats. Everything of our application need to be run at a very high level of integrity and reproducibility. We don’t want to loss trust from users because of the breakdown of the application. We also don’t want to generate extra cost for a wrong decision due to wrong execution of the application. Though we can ensure the application runs properly on our local machine, when the application is moved to a different environment in a traditional way, extra uncertainty is generated and raises doubt on the performance of the application. You can carefully assess the risks and try to fill all the gaps manually, but software container with Docker can completely remove those concerns with less cost. You can quickly package things you run locally and run the package somewhere else in the exact same way, greatly improved the integrity and reproducibility.<br />
<br />
<img src="https://github.com/hong142/MGMT690-Writeup-5-Yuwen-Hong/blob/master/container.png" width="500"><br />
The old way: Applications on host &nbsp; &nbsp;  The new way: Deploy containers <br />
## Intro to Docker
Before we work with Docker, you should get familiar with a few jargons. A ***docker image*** is a package of an app and its dependencies you create on our local machine to run somewhere else. A ***docker container*** is a running instance of a docker image. When you run a docker image, you have a running docker software container, which you can interact with. The ***docker engine*** is the underlying application that builds and runs images. The ***registry*** is where you store and tag all your docker images, so you can push up changes and pull them to different machines. Similar to the repositories on GitHub, registries can be either private or public. ***Dockerfile*** is a file that tells the engine how to build an image.
## “Docker-izing” a pipeline stage (validation)
### Create Dockerfiles
The first step to containerizing a pipeline stage with docker is to develop the application, create a dockerfile accordingly and push it to GitHub. The application script needed is exact the one you normally would develop locally and version on GitHub. Based on last week, one of the stage in our pipeline is image validation. Take this stage as an example, the input is an image, and the application will extract its file name, check if the name has an ID and a timestamp, and then copy the image to either valid directory or invalid one based on the checking result. We’ve created our ["validate" script](https://github.com/dwhitena/mgmt690-pipeline/blob/master/validate/validate.py). You can find a lot reference for creating docker file online. What we consider to use this time is the following 2-line code, which means add our "validate" script (the application) from Python. The "/code/validate" part is the location in the image we assign to the script, so that we can find it easily. You can put your script anywhere in your image, and you can add as many such files as you want. The "from” at the beginning is almost always in a dockerfile, and it allows you to setup a base system and dependencies that someone else already created instead of recreating it each time. For official dockerfiles, you can go to [Docker Hub](https://hub.docker.com/), a public registry of dockerfiles and images. Using existing dockerfiles as base could save us a lot of time comparing to starting from scratch, and usually details about the author and the functionality is well-documented.
```
FROM python
ADD validate.py /code/validate.py
```
### Create Docker images & Upload to registries
The dockerfile is ready, so through the engine we can create an associated Docker image in which we can run the same python thing. New Docker images are uploaded to a registry.
### Deploy Containers
When you want to run a Docker image, you will pull it down from the registry and run it on whatever machine you want. All that machine has to run is docker. As long as it’s in docker, you don’t need install anything like Python, Tensorflow or R in the cloud instance. Most cloud providers have already had dockers on their instances, so often you don’t even have to install docker to pull down things and run your application. 

```
$sudo docker pull <image>
```
If you pull down an existing image, one thing you want to pay attention to is its size. Smaller images are preferred as it’s quicker to get them up. If you look at an officially supported image, usually it pulls in all the stuff the author thought important, but you don’t usually need them all. Take our Python example, we don’t need both Python 2 and Python3, and multiple file systems. Thus, we create another dockerfile for image. This time, we just install Python on the APK system. You can see there are three layers for the file – from, run and add. The more layers you add to your dockerfile, the bigger the file. You can always modify or create your own docker images to get a relatively smaller size, so that you won’t taking up a lot space and time to deal with the images.

```
FROM alpine:3.6

RUN set -x \
    && apk update \
    && apk --no-cache add python3 \
    && ln -s /usr/bin/python3.6 /usr/bin/python \
    && find /usr/lib/python3.6 -name __pycache__ | xargs rm -r \
    && rm -rf /root/.[acpw]* 

ADD validate.py /code/validate.py
```
After we pull down the image, here are three common wanys to run imges as constiernes: 
### Interactively
You can run them interactively, which means run the container and going into the container to do things manually. You'll need this code to call the image, with "it" stands for interactive.

```
$ sudo docker run -it <image>
```
### Command in container
You can also run the container and run a specified command in that container. If you run the following code, you should see a docker command prompt, and now you are interactively inside a running container and you can execute different commands within the ash shell. We used this approach to run our container and we found exactly the same” valid” script we have locally, which we could run it the same way on the cloud as on our local machine without installing anything.

```
$ sudo docker run -it /bin/ash
```
### Daemon
If you had a service such as a Shinyapp or something, you might want to run that as a daemon, which allows you application to keep running without having you to login to the machine repeatedly

```
$ sudo docker run -d
```

 

Completely automated. The model way we have been doinghtings, take out all of the muanl piece you can put pf this process, all of this auptmeated, using contiuse integration coniuts implenetaion.
 You don’t awn tot do tow theoruhg fur manully, we will do some mauclly today. To see how it works. 

  

## What will be different in the actual Pipeline, Kubernetes + Pachyderm


We need to gather output
We want to deploy many containers over a cluster of resources in an optimized way

Build our pipeline stage Docker images like we did today
Have Pachyderm deploy those on top of Kubernetes by telling Pachyderm about the containers via JSON specifications (next week!)  
Kubernetes will automatically schedule and optimize our containers on our cluster.
Pachyderm will get the right data to the right containers and gather/version the output

Following the similar step we did for validation stage, we can docker;ize each stage of our pipeline and run it on some machine without installing a bunch of codes and ependencies. After dploy each of those stages as a doker contierner, we expect to run multiple ocnatienrs. we might also need to deploy multiple containerized “workers” for each stage, Which means we have even more contianers to mange. For example, we might want multiple aowrkers to process incoming images so that this procesing stpe is distributed. 
We need to get the right data to the right containers at tha right time. In our case, we need incoming images to pe passed to a specific contianer and the output to be pass out to another container for each stage.
another. We want to deply multiple contierns across a cluster resoruces, so we mahigt have 55048 machines for a team, somwehow, wewaht to deploy all of those conteianers in an opitmizse way to all of thise nodes. Also being alboe to get the right data rightcode, right time which meanswe don’t wabtnt ooding thigns maunlaly loghong running those, we want to automate running. Ulitliaze dokcer images we just build, pekderm deploy those contieanrse on top of kubernetes by teling perkdoem magamnet /valllited, tell pderkderm you should procees this data with this dokcer imge, under the hood, perkcer will deploy those things for us in an autoa,etilaated way,kubernet will be the things under the hood actually decides you vea taldme to dlepy this contiern, based on twhat already runign on all of this underlying machies, I know they shouel dhere. Because this one already have those thigns rungin on it,.. allows us to mange and scahduel the deployment of a lot constiers all over the same tiem. Pefkearam goint ottkae the data form the data reporistyor we ve cratein the past andget them to the conteirn suhc aht they can process them. Gather the output formthose contianres. 










