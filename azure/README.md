# Azure Functions

## Overview

The goal here is to create a working Azure Function, an online service that factors an integer. 
Because no Virtual Machine is explicitly involved in this process the result is called a 'serverless function'.
This suggests visualizing the cloud as a sort of programmable computer. To do follow the provided 'procedural
please open [`tutorial.md`](https://github.com/robfatland/serverless/azure/tutorial.md) in this folder. 
To understand the contextual basis here please continue reading. 


### What is the purpose of serverless computing? 

We have different degrees of effort involved, cost, and personal interaction potential when we build computing
environments. 'Serverless' is on the simple-cheap-not-very-interactive end of this spectrum. Our contextual goal
is supporting research, i.e. building research infrastructure. Serverless abstracts away 
the concept of an 'underlying computer' where the creation process takes place. This dispenses with preparatory 
effort: Identifying a machien to use, setting up credentials, logging in, perhaps patching the operating system
and so on. Serverless functions (also called 'microservices') are both cheap and (with a little care) very secure.  

One other key point: As serverless functions can exist as public endpoints, or 'URLs on the internet', 
serverless functions can be used by collaborators or by other scientists we do not know. 


### Where do we build?

If a serverless function on Azure is not built "on some virtual machine on the cloud"... where *do* we build it? 
The thought process is that we already have a laptop or a desktop connected to the internet; so we will follow 
two courses of action: First using a browser to connect to a portal; and then second by installing an app, 
an Integrated Development Environment, that is designed for developing and testing serverless functions before
conveniently uploading them to the Azure cloud; where they 'live and operate' as needed. 


This browser/IDE approach could be set up on some other computer as well of course. We won't take that approach
here so the preparation will involve three parts: Making sure you have Azure credentials, making sure you have 
a web-connected browser, and installing the IDE. The IDE we will use is called Visual Studio Code, abbreviated 
VSCode, and it is available for PC, Mac and Linux operating systems at no cost. 


## Additional context


### Approaches to building serverless


The idea is to create a very simple serverless function on Azure. These are called Azure Functions. 
Our example will receive an integer value appended to the service URL and it will return the prime factorization of that integer. 
How to go about building? There are multiple approaches.

- Not used: Command line approach
- Not used: Visual Studio approach
- First try: The 'obvious' path of using the browser-based Azure portal
    - We start here to get a sense of how the portal works...
    - In passing we also notice how *templates* would work 
- Second attempt: Switch to **Visual Studio Code** (VSCode), in integrated development environment (IDE)
    - This is free and includes free installable *extensions* that connect with Azure
    - Among the advantages of the VSCode path are...
        - VSCode allows me to develop and test on my local machine
        - VSCode has a simple "deploy to Azure" mechanism
        - VSCode manages authentication so I do not have to constantly be logging in
        - VSCode integrates with containers


### The cloud exists


The cloud (someone else's computers) exists as a computing platform. I myself or someone has to pay for it; 
and we need to make sure that work is not lost. Once expense and reliability are covered the great advantage 
is that much (not all) of the "stuff I have to do before I can do actual work" is handled by someone else. 


### Services exist


Continuing the thread of simplification: A *service* is some facet of computing that the cloud provides. There
are three basic services to start with: Networking, compute power and storage. Each of these has an accompanying
learning curve. For example *compute power* generally means finding and using a **Virtual Machine** or **VM**; with an
intrinsic operating system. If there were some way of ignoring VMs and operating systems and just creating some
code that runs on demand to perform a task like say a Fourier transform: Perhaps that will simplify life. This 
introduces the idea of serverless computing. "Serverless" means "no identifiable computer or Virtual Machine is 
involved". So while it sounds a little odd we can say "Services exist on the cloud, including serverless
compute services."

Note in passing that working with the cloud implies a network connection. So the internet is a key element of
this new process; and so this has to work and we also want to avoid overloading network communication capacity
by moving a lot of data. Since the data is intended to be in the cloud, this suggests moving the computing into
the cloud as well. Within a particular cloud the networking is managed by the cloud provider and it is very 
fast; so this is another aspect of cloud design philosophy: Bring the heavy computing to the data and keep
the communication rate from my local computer to the cloud very modest.


### Triggers


A serverless computation is said to be triggered: Something makes it run. This might be an alarm clock set 
up as a trigger. It can be a new data file arriving in a storage location, again pre-configured to act as a 
trigger. And thirdly, as in this tutorial, a trigger can be an HTTP request. 


### Secure versus Public


Incomplete. The idea here is to describe the use of environment variables so that source code can be openly
published without risk. 


### Build how?


We have identified a working environment, the cloud, and we suppose there is something we would like to build
thereon. How to do this? Not surprisingly there are multiple paths. First we can log in to the cloud control
console. For Azure this is called the *Azure Portal*. The problem is that there are many details that the portal
must canvas, making it something of a learning curve unto itself. So in the interest of choosing our learning 
curves we will take a moment to "try this out" but we will quickly migrate to a different learning curve as our
main approach. This new learning curve is to install and use an Integrated Development Environment (IDE) called
*Visual Studio Code*. This application runs on any sort of operating system and works well as an answer to 
"How shalle we build things?"


### Build what? 


Let's describe our premise next. We want to run an algorithm as a serverless service on the Azure cloud that simply
factors an integer of our choosing on request. This means that the service (in the cloud) will be reachable over
the internet by means of a URL. This could be an *ip address*-style URL like `https://123.123.123.123`. Or it 
might have a friendlier URL such as `https://factoring.azurewebsites.net/api/factoring`. Whatever the case: Our 
intention is to write and test the service on a test machine -- in Python -- first. And for the sake of 
anticipating future work where things get more complicated than factoring: Let's insist on using not one 
but *two* code files: The main program file and a second file containing a factoring function. 

Once this is all sorted out (meaning that it runs properly on our local computer) we will deploy the service 
to the Azure cloud and test it out there. In order to test it we need to give it a number to factor; and this
is conveniently done by appending the number onto the end of the URL. Suppose we want to factor 15 and the
service URL is as above `https://factoring.azurewebsites.net/api/factoring`. Then what we are going to type
in the browser address bar adds a `key=value` string. The key is the letter `n` and the value will be `15`. 

With this as a contextual starting point, the idea is we are now ready to build. This is written up in
the `tutorial.md` file. 
So the full URL will be 
[`https://factoring.azurewebisites.net/api/factoring?n=15`](https://factoring.azurewebisites.net/api/factoring?n=15).


Replace the `15` with some other number to double-check we're doing an actual calculation!
