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

Moreover, we are running an analytics project and we expect the application to make decision by itself, such as make detection and inform customers with threats. Everything of our application need to be run at a very high level of integrity and reproducibility. We don’t want to loss trust from users because of the breakdown of the application. We also don’t want to generate extra cost for a wrong decision due to wrong execution of the application. Though we can ensure the application runs properly on our local machine, when the application is moved to a different environment in a traditional way, extra uncertainty is generated and raises doubt on the performance of the application. You can carefully assess the risks and try to fill all the gaps manually, but software container with Docker can completely remove those concerns with less cost. You can quickly package things you run locally and run the package somewhere else in the exact same way, greatly improved the integrity and reproducibility.
## Intro to Docker
### image
For Docker, an image is a packeage of an app and its dependencies you create on our local machioie to run somewhree else. 
### container
A docker contianre is a running instance of docker image. When you run a docker image, you have a runign docker sofetwera container, which you can interanct with. 
### engine 
- the underlying application that builds and runs images
### registry 
registriy is where you store and tag all your docker images, so you can push up changes and pull them to different machines. Similar to the repository on Github, registry can be either privivate or public. 
### Dockerfile
a file that tells the engine how to build an image




Upload the image to a registry
Deploy a Docker container, based on the image and from the registry, to a VM
## “Docker-izing” a pipeline stage (validation)
### Create Dockerfiles
The first step to dockerlizing a pipeline stage is to develop the application and create a dockerg=file accordingly. If you go and sercah for feference for creating docker file, format things here, speficy a buhnch of things. 2 lines long docker file, from python, add this thing. On the line left. Varies key word, form add into specify environmental runsomthing. “from”,pretty much always see this in a docker file, it allows you to setyup like a base systmes and denpendecy that someone else already created. ,you don’ have to reacert eucyehtime. Official python docker image, docker manticnie this, you go to docker hub, this is dokcer hub, a public regristy of docker iamges, someogpf them ofically supported by docker, if I writoe my python thing, what base image I can use, so I don’t have to do it form srpth. Serch for python, a whole buch of them, this one says official, you can see it has been pulled and used for over 10 milion times. Click, more details, refrence, who created it. What’s in it, how it run a python script, go form that python docker imge, I’m assuming you cloaudl read ablut the image, but the already has pyhtonin it obvliuosly, so I’m assuming all I need is to add my pyton sript. The I’kl be able to run it with python in the image. The I just add my python script to the image, and Imoveit to the location whre I nat it in my image, here /code validate.just so ai CAN FIND IT ESILY. You can put it anywhere, you can add as many this file as you want. Slielrien images, samw way.
Develop application,Create Dockerfile, and Push to GH
Write your python code, do all your stuff locally like you normally would versionthat in github. Next build this iamge, packe for your application using docker engine. The uplod that image to a registry. Where commly case by all your diferent nodes in the cloud everything. Whe you wna tto run that, you will pull down that docker imgae froma registry and run it on what enver machine you want run it on. All that machine have to run ti is docker. Letirally, whte you are running tendsflow, r, as long as its in docker, all that needs to be runon that machine is docker, you don’t need python or r . this would be the cloud instance. That’s no need to dep;oy anything anywhere, except docker. As lonas docker is running somewhaer, you can just pull down dan run it., whre tis tendosfow or jave, whatereve. Only dependecy. Most any of the cloud providers have instance your launch that alraebdty have dockers on them ,this is thevery commong things on this point. You just choose the dcoekr enabled intscne you don’t even have to install anything you just pull don dna running. 
One of the nice thigns this pakceas are realtively small, most of the lag that hapneds . if you installing an iamge its like 500 mg, it sgonna be a aligt lag when you first time pull the imga and runit locally. No real lag. Varies things you need to be aware aroung, security. 
The onlything you wold not want to do is like if everyhitng time you ran your  container you want to like pull or configer a file from here, you may want to just ad it to container, not pull it everytime,
Completely automated. The model way we have been doinghtings, take out all of the muanl piece you can put pf this process, all of this auptmeated, using contiuse integration coniuts implenetaion.
The process is you develop youor application, creates a docker file for that application, puch it up to geibud, and there is a system listeing to gethub and when you push a change, the it automatically does stpes tow theough four, based on that things, it knows where your docker file is so it build it and it has your dockerimage,so it update the registry. And it knows potantilaly whre to deploy that, so getdeploy to the machine. You don’t awn tot do tow theoruhg fur manully, we will do some mauclly today. To see how it works. 
Based on last week, start careteinghtis pieces for piepiline, …,python scrip does the simple verison of validation. Input file, an image, extrat file name, userid,timestamp. We slit that up to ake sure we have a first device id, seocnly a tiem stamp, in uinx, at leat 10 digits, just checking tha. Based on I decidew its vaidlet or not, that it copy to either the vlaid output directory or the inviald output detrictory. Cheing the name and passing to to the other. 
Create a file deviceid , timestamp, go a head and vialid that nme. Creates a viald derictory and an invlaud derityo , now I have tow dierctories here .locla machiene. Provide the file, validating the tag
Now its inside the vialid dercotry, nothing in invialid derictory. Crat an inviald name, tyr that, that oei sin invlaid deirctory. Function naly we nned in the firt sbit of our pirpiela.
### Create Docker iamges
The dockerfile is realdy, so the egnigen knows how to create an associated Docker image in which we can runthe same python thing. docker engine is runign locally

Dokcer build, give it a nmane, tag, seppoicifc format, /ui/mangt/vlaidtae, specify username on the registry, next parat is the name what I want ot call the dockert image. You can have difrent version of the image. Fingd the docker file her ein this derctory and build th eiamge fromthat one. 
If I don’t have the docker python image dowlaoded, look nad see , I hav eth following docker images in my loacla reafiacltiry , machine. Push upto the registry, push and name. 
If I look at this officlay supported python image, medium size, small doker images are prefred, easy to port them apriund. Quicker to get up and run,3-4mg, when you use preexsitng iamges like this, base imageI just pulled in all the stuf they though was important in tha tpythonbut Idon’t actually need all that stuff, to run my script, the likely have pit instlled and probebaly python 2 an dpython 3, and a whole buch of stuf which idont realy carea bout.carete another docker file , this is a slightly complicated example, insteaded  f using the base python image, which is based on ifel 2 system, uotitme lineix, another system really small. I want  my imge to be 300 mg, I want run form ourpie, and all I did I added a run commad, and in this run command, I just install python, depending on what system you are y=using, like here is apk, if you using uptwo, you are using appget, what I woulr run to install python on that system. If you running form upontow and you want to install syekterlean, you could do pip and install selvylern. So I install python in this case, do alit bit clea up and adding my valideate script, and build that here I’m specify difernt docker file, and IC AN PUCH THA TONE UP. You can seein the other one, more layers wereexsted, so doecker file are build up in layers, if I look on this docker file, done three things, from around andadd, those are three seprate layers, the more layres I add the biger the docker files get, if I workd over here, go back, I have two tyoe verions of this, and the boger 275MB, IF I USING A  smaller file sytme, smaller base imge, 30mb. Super usful for prevernt you form taking up all the spakce on the machine, taking a lot of time doawning , lag, doeing images. 
## Hand on! Working with Docker
Build iamegs, run them. Another IP.know bug, maixmuize the windeo get bush, nit maxime the window use putty. Ssh wkshop@, 
Machine with doecker on it. Mauly be ;going in againrunignthis comnads, but this are the simleiar  command s wewill be runign the way to get our docketr container up ad nruning. Check, sudo doekcet images, password again,oemhtinglie this, turns on this mahcinae, thers alrady a few images onthis docker macher. Some fo the base iamges. You ca lsow, what docker iamges are runign, by saying sdo dokcer es, nothing s runign yet. Lokk whatiamges I have locally, what iamges are running. We odn;t have the image I creataed earlier. If I ls to see,  theres nothing, I have;r copuyover my python sript, I haen’t copy over any dependencies or any spesifice verison of pytihon.  So in oerder to run our validation on this machine, what I don’t  want to do is to maualy ocpy over all the ithigns hre, what I need to do is to pulldownthat dokcer image and run it . sudo docker pull dy/mgmt./validate/slim, nice and small. Go get that image that duanle build ob=n his mahciena and oush up that resgirry. If I do tha t,it will pull it down , uyou should se something happned lie that. Sudo dokcer iamges, now icna ise ihave that image lovally, now ive used docker eignieg to pull that image, imcludes the python I want urn , and I pull it to this machine, I didn’t do anything else, dint; install anything, on this maohcien,except docker. Each is connected to Indiciaudla instialce on the cloud runign doker. Typically on a team what you have is a shraed cluster resoruces, you math have tewn or 15 intances and you want ot deploy abnouch of difernt things to those instances, so in mals sace you might just have one thing on one instances, mulitpmg things for multiple peple runign on the same instances. We have iamge lovally now. Run r sypython that machine, instead of install maunlaly, run docker image as a container on this machine.
Sudo docker run, specify the image I want ot run, I could runjust like that, 
Here are three common wanys to run imges as constiernes: 
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










https://github.com/dwhitena/mgmt690-pipeline/blob/master/validate/validate.py
