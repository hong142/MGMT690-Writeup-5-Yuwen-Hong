# Containerizing analyses
Last week, we designed our full security image object detection pipeline, which includes stages of preprocessing, object detection and postprocessing. This writeup discusses the software container, the last piece we need before we deploy the whole pipeline. Specifically, we are going to briefly introduce Docker, the most popular software to facilitate container creation for data analytics. We will containerize one of the stages, validation, to show you how Docker works, so that you can deploy similar things.
## Why software containers?
When we have an application build, we essentially want to take this application off laptop, and get them running on the cloud or some instance in the cloud. For our case, it would be Python application and ec2 of amazon. To achieve this, typically we have four options. 
### One app per instance
The most straightforward way is to install one application per instance, and install its dependencies directly on that instance. That means every time you want to run an application on a certain machine, you will need to manually create an instance and copy over code and dependencies. The process is inefficient in terms of both time and space.
### Multiple apps per instance
Likewise, you can install multiple applications together with their dependencies directly on one instance. However, in addition to the issue of inefficiency, this approach also raises issues of duplication or even conflict because not all the dependencies you want to run on the same machine coexist nicely. For example, if you have two applications, both running scikit-learn, then typically you will install scikit-learn twice. What's worse, if two applications are running different versions of scikit-learn, then you may encounter collisions.

### Virtual Machine (VM)
what can run on what. Where. 
Then, to overcome prevoius problems, we get the thirid option, vitural machiena(VM), which cna be one or many per instance. Vm came to partition fo all the stauff we need, TUN THEM ON THE SAME MACHINE on virtual envirnmnet. Vm hundenben, spinning that vm on your local machine, specify allocaio of discs space to my vm , os that is can do what it needs to od. Nost of the time,  you probablry not using that much RAM or the sapece, and you are not even use it all the time, but nothing else can sue this. Not very efficient resource-wise beacuse tihs automaticallty limits how many things you can run on this machine. Moving a thig around all the time and constantly update it,  between machine or a new machine, it’s a lot data transfer, slow in genral. Each cloud instance we are takiling about here is a vm, you can run multiple vm on your location machine, each one have difernt diffendencies insdie it. 

### Containers!
which eosen’t rea;ly slove the optiomiztion of resource. Using containers. Automatilating , coarbing out vm for each thing running. Container, similar.
New way, you carete this rather small pacjages that includes your application and its dependencies and the files systems, can operate on. No matter what’s insiede them are trated the same in keener.share the underly kernel. Underliy memeroty, disc usage, cpu. If one isne’t using up memory, that’s all free for the other one to utilie. 

Docker, interface to contianres which is usefraely and esy to use. Docker provides certain implementation of software containers. Diference between vm and softwrare contirens. Vm an entire operation sytem along with denpendcneies you need and aplicaiton. In the docker, smaller packeages, which just inlduces your application and enay denppendices your applciaiton nneds, and all of those run on the saem underlying engine.docker engine, which winterfaces with the underling operating system,  mages sharieng pf resioures, and running pakcaes. Right, all share resouces on operaitn gsytem, left cruveut a prituailt space and clus the gust oprating system, whichmakes them so huge. 
In addiotn to those advatages common to any software. advantage In teram sof ata analytics project. We are writng things suppose to make decision thmesleves ;like detection , imofrm people such they can make decisions. Things needs to be run at a very high level of integraty and repordibility. As soon as th application break down, people will never thrust it again if you intedn to make deciosn . thers human factor, there s also complicance factor, you reject some on efor insurance base on some model. A lot loss coming around htat which are incerdibaley scary. On yourlocal machine , you have go through those process likeinstlling things, getong configutation. Combaing with the fact you get somthign running in that envrronmentthen you mave to mveo it somewhere else. And go through the same steps, possibly different ones, because it’s a dfirent environment, automatically introduced a leyof uncertainty and how that applicaitongoing to perfm, unless you are very very caerful adds extra risk into how you applciaitons gonna performe, comnibI THTHE thefact hat a lot things we use are norlike famila=r to software enginerres genarlly, inorganization. R tenforslfow, genrally the sofrwate gienres in the organization have no idea about it. People could possibly halp you in depoy this things often time.. by being able to very quinly in a protabl way pakcege of your thing, and run thst thing locally and then be able to taking that package and run that somewhaer else in the extact same way, with automate certainty it going to behvie in the exact same way, is a huge advantage go for reprodubility.
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
The dockerfile is realdy, so the egnigenknows hoe to create an associated Docker image. docker engine is runign locally
Build a Docker image for the app with Docker engine
Creates a docker image , in which we c an runthe same python thing. To creat our dokcer image her, here we have docker runingon my machine, .  
Dokcer build, give it a nmane, tag, seppoicifc format, /ui/mangt/vlaidtae, specify username on the registry, next parat is the name what I want ot call the dockert image. You can have difrent version of the image. Fingd the docker file her ein this derctory and build th eiamge fromthat one. 
If I don’t have the docker python image dowlaoded, look nad see , I hav eth following docker images in my loacla reafiacltiry , machine. Push upto the registry, push and name. 
If I look at this officlay supported python image, medium size, small doker images are prefred, easy to port them apriund. Quicker to get up and run,3-4mg, when you use preexsitng iamges like this, base imageI just pulled in all the stuf they though was important in tha tpythonbut Idon’t actually need all that stuff, to run my script, the likely have pit instlled and probebaly python 2 an dpython 3, and a whole buch of stuf which idont realy carea bout.carete another docker file , this is a slightly complicated example, insteaded  f using the base python image, which is based on ifel 2 system, uotitme lineix, another system really small. I want  my imge to be 300 mg, I want run form ourpie, and all I did I added a run commad, and in this run command, I just install python, depending on what system you are y=using, like here is apk, if you using uptwo, you are using appget, what I woulr run to install python on that system. If you running form upontow and you want to install syekterlean, you could do pip and install selvylern. So I install python in this case, do alit bit clea up and adding my valideate script, and build that here I’m specify difernt docker file, and IC AN PUCH THA TONE UP. You can seein the other one, more layers wereexsted, so doecker file are build up in layers, if I look on this docker file, done three things, from around andadd, those are three seprate layers, the more layres I add the biger the docker files get, if I workd over here, go back, I have two tyoe verions of this, and the boger 275MB, IF I USING A  smaller file sytme, smaller base imge, 30mb. Super usful for prevernt you form taking up all the spakce on the machine, taking a lot of time doawning , lag, doeing images. 
## Hand on! Working with Docker
Build iamegs, run them. Another IP.know bug, maixmuize the windeo get bush, nit maxime the window use putty. Ssh wkshop@, 
Machine with doecker on it. Mauly be ;going in againrunignthis comnads, but this are the simleiar  command s wewill be runign the way to get our docketr container up ad nruning. Check, sudo doekcet images, password again,oemhtinglie this, turns on this mahcinae, thers alrady a few images onthis docker macher. Some fo the base iamges. You ca lsow, what docker iamges are runign, by saying sdo dokcer es, nothing s runign yet. Lokk whatiamges I have locally, what iamges are running. We odn;t have the image I creataed earlier. If I ls to see,  theres nothing, I have;r copuyover my python sript, I haen’t copy over any dependencies or any spesifice verison of pytihon.  So in oerder to run our validation on this machine, what I don’t  want to do is to maualy ocpy over all the ithigns hre, what I need to do is to pulldownthat dokcer image and run it . sudo docker pull dy/mgmt./validate/slim, nice and small. Go get that image that duanle build ob=n his mahciena and oush up that resgirry. If I do tha t,it will pull it down , uyou should se something happned lie that. Sudo dokcer iamges, now icna ise ihave that image lovally, now ive used docker eignieg to pull that image, imcludes the python I want urn , and I pull it to this machine, I didn’t do anything else, dint; install anything, on this maohcien,except docker. Each is connected to Indiciaudla instialce on the cloud runign doker. Typically on a team what you have is a shraed cluster resoruces, you math have tewn or 15 intances and you want ot deploy abnouch of difernt things to those instances, so in mals sace you might just have one thing on one instances, mulitpmg things for multiple peple runign on the same instances. We have iamge lovally now. Run r sypython that machine, instead of install maunlaly, run docker image as a container on this machine.
Sudo docker run, specify the image I want ot run, I could runjust like that, coupled difernt wany to run imges as constiernes, 
### I can urn them interactively, run th ocnteianer and going into the ocntianer to do abuhc of stuf maunuly. 
Run /it menas interactive,
### OrI can run the contienre an druna comnad in that container, speficy ocmamd. 
/dan ash runinga commd in this contiern, means run an asher show in this comtirnaer, such that I can dod stuff in the ocntienr. If you run that, youshould have those litt command promp, there is a docker contierne running onthat machine, and we are intercltiveliy isnde that contaidner. Where wecan excecute diferent commnds. Ls seethis code directory, whre I put code to imgae, if I cd code, ls, I can see valisdete things. Exact same vailde script I have locally, can runit the same way. Tuhc lod jpg. Make duern. The behaiveor that I programed locally, run that eact functionaly now. Without instialling anything, just copy over that docker iamge, exit the ocntianre, not the machine.
### Runtha tcontierne as a dimen, which is if you had a service like a shiny app or something, you want to run tha t as a demdn, such that you run it a nd you don’t have to be login to the mchien to continue to run , it just keeps running. 
Dokcer call jupertnotbook.dont’ have juptyoer instlled, pull this iamge, rather lager, strgiht around jytoer server and coonect to it. Sudo docker run /d for demen. Keep online. Expose a port the juyper running on as its runign on my local machine. 
## What will be different in the actual Pipeline, Kubernetes + Pachyderm
We might need to deploy multiple containerized “workers” for each stage
We need to get the right data to the right containers
We need to gather output
We want to deploy many containers over a cluster of resources in an optimized way

Build our pipeline stage Docker images like we did today
Have Pachyderm deploy those on top of Kubernetes by telling Pachyderm about the containers via JSON specifications (next week!)  
Kubernetes will automatically schedule and optimize our containers on our cluster.
Pachyderm will get the right data to the right containers and gather/version the output

We can docker;ize a python thing and run it on some machine without installing abuoch of caropy, what we want, why we need anything eles, we gonan use a copuple of difert tolls next week. Why nned those otlsl in adtionan to docker. Dpeicfally, we have a buhch of pipieline stages, we are going to dploy each of those stages as a doker contierner, we wre going torun , multiple ocnatienrs,, also for each of those stages, we might wnt run mulpler workers of that stage, so we might want multiple aowrkers processing my incoming images so I can distribute that provessing. Which means we have even more contianers to mange. Wealso nned to get the right data, the need to be proceed to the roghte ocntoenters, at tha righttime. So when imges coming in I want those, to be pass out to a contier and the output to be passed to a difernt contiaer. .. another. We want to deply multiple contierns across a cluster resoruces, so we mahigt have 55048 machines for a team, somwehow, wewaht to deploy all of those conteianers in an opitmizse way to all of thise nodes. Also being alboe to get the right data rightcode, right time which meanswe don’t wabtnt ooding thigns maunlaly loghong running those, we want to automate running. Ulitliaze dokcer images we just build, pekderm deploy those contieanrse on top of kubernetes by teling perkdoem magamnet /valllited, tell pderkderm you should procees this data with this dokcer imge, under the hood, perkcer will deploy those things for us in an autoa,etilaated way,kubernet will be the things under the hood actually decides you vea taldme to dlepy this contiern, based on twhat already runign on all of this underlying machies, I know they shouel dhere. Because this one already have those thigns rungin on it,.. allows us to mange and scahduel the deployment of a lot constiers all over the same tiem. Pefkearam goint ottkae the data form the data reporistyor we ve cratein the past andget them to the conteirn suhc aht they can process them. Gather the output formthose contianres. 










https://github.com/dwhitena/mgmt690-pipeline/blob/master/validate/validate.py
