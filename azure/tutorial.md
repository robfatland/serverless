# Azure Functions for scientists and students: A walkthrough

## Construction notes


Need to factor in; [Appendices](#appendices) maybe?
### Before we get started...

* [Appendices](#appendices)
    * [Appendix on Docker containers](#docker-for-the-uninitiated)
        * A [remark on WSL 2 for running containers on Windows](#understanding-docker-in-relation-to-wsl-2) 


***What are we doing? And why?***


* Answer: We’re building an Azure version of a serverless function in Python 3 
* Result: Testable URL + context: Building operational research code on the cloud, lightweight version
* Consider this **S4** mnemonic: Azure Functions are **$cheap, serverless, secure and scalable**.
* Cost: Free for the first 400TB / one million executions. 400TB of what???? (kilroy)
* Serverless structure: Trigger → Code runs → Output


***Why Azure? Why Serverless?***


* Azure: One of four big clouds. Many well-designed, proven features.
* If you are somewhere in the vicinity of ‘code-writing data scientist’: Azure has a great integrated environment that makes you super productive.
* A good term to learn is infrastructure: the scaffolding of a computing system… 
    * (Folks who think the infrastructure is the interesting stuff are your friends!)
* For a business: Put a Service on the web! Now you can make this using Serverless technology. It will automatically scale up (makes copies of itself) to meet customer demand. "Nobody ever had a bad waiting experience using your Service. Five stars for you!"
* There are many (free) resources on the web to help you learn Serverless


***To Add List***


* Define a RG better
* Could use a punchline to the portal segment beyond ‘yay it deployed’
* name all the moving parts of an (empty!) AF
* printf(‘this is a debugging statement’) minimum viable debug nod
* 3 listed entities in the RG for the AF
* Do not break the recipe e.g. where __init__.py resides onless ye know
* In VSCode: Can manipulate the layout in menu bar to isolate FUNCTIONS
* AF wizard: Portal: Implications of Anonymous versus…
* Add something to requirements.txt and use that in a simple manner
* Could use more support for Window Python 3.8+ 64 PATH: How to set? Also diff between Path and PythonPath is what?
* More on automation via “provide template”
* On trigger: GET, POST and the example code has also the “body parse” > Appendix
* Close the loop: Go back to Azure and identify AF + RG + SA for new AF
* Give Output examples: an HTTP response, a new file stored in some particular spot (SA)
* Think about / cover the import of private environment variables; and related: Rationale: How do we get our AF code on GitHub safely? There is already a lot of machinery in place in VSCode. 
* More #foo and more chopping sections into sub-blocks
* [Microsoft documentation part 1](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference)
* [Microsoft documentation part 2](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-python)



## Overview


We walk through building a simple serverless utility on the Azure cloud called an *Azure Function*. 
Since we publish code with no contact with a virtual machine en route this kind of service is
called 'serverless'. There is a server but we just don't worry about it at all.


**A note on using the Azure portal**: Portal procedure is covered in part 2 of this walk-through. If for any reason you
are not interested in working with and learning about the Azure portal you can do part *1*, 
skip sections *2* and *3* and continue with 
[*section 4*](#4-switching-to-visual-studio-code).


**A note on 'Development System'**: Your Development System is a computer including its operating system;
where you develop code for deployment to Azure Functions.
It may be your laptop; it may be a Docker container; it may be a cloud VM; it may be a large pile of coconuts. 
Whatever it is you will need this Development System to follow along on this walkthrough. In parts 4 and 5 we 
describe what you will need to install there; so please go through that in preparation. 



## 1 Establish access to an Azure Subscription 


Make sure you have access to your authentication credentials; and take care that they do not end up in 
a public location such as GitHub. 


The construction we do in this tutorial will cost very little (as in pennies) but to
minimize any cost impact and/or risk it is a good idea to delete everything when you are done. 


## 2 Portal work


On logging on to the [Azure portal](https://portal.azure.com) you see the home page, the upper left corner looking something like this: 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/00_1_Azure_portal.png" alt="drawing" width="400"/>


Using the portal is something like a "what you see is what you get" interface experience. All of the complexity
of using the cloud is there; so the idea of starting out here is to do a bit of context learning through immersion. 
As noted above this can be skipped entirely in favor of moving ahead to step 4 of this walkthrough.


A 'Resource' is the generic term for an item you'd like to allocate and use. A 'Resource Group' is a logical container
for resources that are associated with one another (in your view). We treat a Resource Group here as just simply 
"where everything we build goes". 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/10_1_Create_a_resource.png" alt="drawing" width="200"/>


Choose ***'Marketplace'*** rather than ***'Custom'***. The Azure Marketplace is a large collection of cloud resources 
and services; it is where we find things like Serverless Functions and Virtual Machines and so on. 


Type 'function' in the search window to help select the resource you want: An Azure Function App. 
   

<img src="https://github.com/robfatland/serverless/blob/main/azure/images/10_2_Choose_Azure_Function_App.png" alt="drawing" width="200"/>

<BR><BR>
   


Click on the **Create** button. We proceed to a create *wizard* process: We go through each of a series of tabs
entering key information. At the end we click another ***Create*** button so that Azure starts up our Function.
From that point on the Function will exist (it even has its own URL) until we choose to delete it.


<BR><BR>
   

<img src="https://github.com/robfatland/serverless/blob/main/azure/images/10_3_Create_Function_App.png" alt="drawing" width="200"/>

<BR><BR>
   


The first tab of the Create wizard is **Basics**. Details we fill in:

* **Resource Group**: Choose the drop-down default
* Check **Code**, not Docker Container (that was the previous tutorial)
* Choose **Python** and **3.8** 
* Choose region **US West** as it is geographically closest (fastest connection speed) 


Note: The Resource Group drop-down should show something like 
`rg-amlclass-netid`. This is your default Resource Group, it already exists, so it is an easy choice.

<BR><BR>
   



<img src="https://github.com/robfatland/serverless/blob/main/azure/images/10_4_Function_App_Wizard_Basics_Tab.png" alt="drawing" width="700"/>

<BR><BR>
   


For each Wizard tab -- once it is filled in -- choose the right-most option at the bottom of the page
to go to the next tab. Don't choose **Review and Create** as this skips
over the intervening tabs. 


Next is the **Hosting** tab. This should resemble the one shown below. Notice that a Storage Account is created by default and associated
with the Function App. A Storage Account is a logical container like a Resource Group but specific to data storage modes
on the cloud. 

<BR><BR>
   


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/12_1_Function_App_Wizard_Hosting.png" alt="drawing" width="700"/>

<BR><BR>
   


Next comes the **Monitoring** tab. This offers to turn on a service called Application Insights. This is a tag-along service
that helps track resource usage: For understanding behavior and debugging problems. We will skip it; leave it Disabled (No).
There may be a message saying we can't use it in any case. 

<BR><BR>
   


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/13_1_Function_App_Wizard_Monitoring.png" alt="drawing" width="700"/>


<BR><BR>
   
The **Tagging** tab allows us to annotate this Azure Function with key-value pairs. This helps immensely when trying 
to remember what the Azure Function does after not thinking about it for four months. 

<BR><BR>
   

<img src="https://github.com/robfatland/serverless/blob/main/azure/images/14_1_Function_App_Wizard_Tagging.png" alt="drawing" width="700"/>

<BR><BR>
   

The final **Review / Create** tab allows you to launch the creation process. Look over the summary and click on **Create** at bottom left.

<BR><BR>
   

<img src="https://github.com/robfatland/serverless/blob/main/azure/images/15_1_Function_App_Wizard_Review_Create.png" alt="drawing" width="500"/>

<BR><BR>
   

If all goes well you arrive at a confirmation message.

<BR><BR>
   

<img src="https://github.com/robfatland/serverless/blob/main/azure/images/16_1_Function_App_Wizard_Deployment_Complete.png" alt="drawing" width="600"/>

<BR><BR>
   

Now let's take stock of the Azure Function: Click on **Go to resource**.

<BR><BR>
   

<img src="https://github.com/robfatland/serverless/blob/main/azure/images/16_2_Function_App_Overview.png" alt="drawing" width="600"/>


<BR><BR>
   

There are four things to note here


* The upper left corner features a link to return to your Home view
* The left sidebar title is the name of your Azure Function
    * This means that your current view of the Portal is focused on your Azure Function
* The scrollable left side-bar is a context menu for the many facets of the Azure Function
    * By default *Overview* is selected and this is what we see in the main-central panel
* Along the top of the top of the main-center Overview pane is a series of common tasks: **Browse**, **Refresh**, **Stop**, **Restart** etcetera
    * These refer to the state of the Azure Function
    * Note that the Function can be stopped without it being deleted


Over to the right in the main panel is the URL associated with the Azure Function

<BR>

<img src="https://github.com/robfatland/serverless/blob/main/azure/images/16_3_Function_App_URL.png" alt="drawing" width="600"/>

<BR>

Click on the URL. This should open a default page in your browser.  This is what an Azure Function does when we do not give it a 
specific job to do (code to run). We verify here that the Function was created and works, albeit it is not too interesting at this point. 


Now let's return to the portal Azure Function view and navigate up a level to **Home** and then down into 
the default **Resource Group**. Click the **Home** link at the upper left, 
then click the resource group **rg-amlclass-netid**. Here we should find a listing of the resources in 
this Resource Group including the Serverless Function we just created. 

<BR><BR>
   
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/16_4_Resource_Group_Overview.png" alt="drawing" width="600"/>

<BR><BR>
   

This concludes the creation of an Azure Function using the Azure portal. 
While the Azure Function is not terribly inspiring we have accomplished two things:
We established a "Portal view" of the cloud resource creation process;
and we have done some navigation of the Azure cloud via a web browser. 


## 3 Aside on templates and code


In the portal Overview of the Azure Function at the far upper right is a hyperlink
that reads **JSON view**. It is fine to click this link. The result is a pop-up
text document that describes the creation of your Azure Function using a descriptive
protocol called JavaScript Object Notation (JSON). This text can be used as a template
to auto-generate Azure Functions, i.e. do this same task in an automated manner. 


## 4 Switching to Visual Studio Code

We now depart from the Azure portal in favor of a local development application 
called Visual Studio Code, abbreviated VSCode. On the one hand looking at the Azure
cloud through the portal gives a pretty undisguised view of the fact that cloud computing
comes with a lot of details. However it would be nice to work in a more streamlined
environment without the profusion of detail; and that is just what VSCode is.


Install if necessary and start up the Visual Studio Code (VSCode) application on 
your Development System. 



## 5 Installing extensions, Python, and so forth


* Make sure you have Python 3.8+ 64-bit installed on your development system. 
    * This avoids a miasma of inexplicable *does not work* outcomes.
    * Ensure also that your PATH environment variable is set to run this version of Python
* You can update `pip` using the command `python -m pip install --upgrade pip`
* Install the Python `requests` package
    * `python -m pip install requests`
* Install *Azure Core Tools* *from within* VSCode 
    * See [this webpage](https://github.com/Azure/azure-functions-core-tools#installing)
    * This introduces using the Action Bar in VSCode: Vertical icon bar at far left



Note: If you are working
in a pre-built environment such as a Docker container: These things may already be installed. 



## 6 Creating an Azure Function in VSCode

### Prefatory on VSCode


VSCode starts with a tabbed Welcome page that you can dismiss. Doing so reveals some Ctrl-key shortcuts. 


The VSCode console layout includes an **Activity Bar** at the extreme left (a vertical sequence of icons) and a **Sidebar**
adjacent to this. Your **Activity Bar** selection will determine the dropdowns and options that appear in the **Sidebar**.
At the bottom of the VSCode console is a status bar. This will include your sign-in status. If you see 
*Azure:yourusername@yourdomain* you are signed in. 


With VSCode running use ctrl + j to bring up a tabbed console in the lower part of the IDE. This acts as a kind of control
center while the panel above it is a tabbed file editor. 


If you are not currently signed in to Azure: 
Type ctrl + shift + p to bring up the Command prompt. Type `Azure Sign In`, select, and authenticate. You should now be
authenticated into your Azure account from your local (Development System) instance of VSCode. Verify this by
looking at the status bar at bottom of the console. 
 


To engage with the Azure cloud and Azure Functions using VSCode: Click the A-like or tent-like Azure icon in the activity bar.
It looks like this: 



<img src="https://github.com/robfatland/serverless/blob/main/azure/images/22_1_VSCode_Azure_tent_icon.png" alt="drawing" width="80"/>


### Preparing to code the Azure Function


For the first part of the Azure Function coding process we do not connect with Azure. Rather we build and test an Azure Function locally, on our
own computer. To start this process: First select the Azure context as described above, then hover your cursor over **FUNCTIONS**
in the menu strip. This is also on the left side of the IDE, adjacent to the symbol strip at the far left. This hover action 
should bring up a *Create new project...* icon, a folder with a lightning bolt that looks like this:


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/22_2_VSCode_new_project_folder_icon.png" alt="drawing" width="80"/>


Create the host folder and make the following choices to configure the project: 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/23_1_VSCode_config_wizard.png" alt="drawing" width="600"/>


This will configure an environment to match these choices and create the project; takes a couple minutes.


Activate the explorer by clicking the double page icon at the top of the activity bar. You should see an item corresponding to your new project. Expand this view.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/24_1_VSCode_Azure_Function_file_explorer.png" alt="drawing" width="200"/>


The `requirements.txt` file describes packages that are installed in a Python virtual environment for the Azure Function to run.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/24_2_VSCode_requirements_dot_txt.png" alt="drawing" width="800"/>


Related: The file `pyvenv.cfg` describes the local Python executable path and version. Above in part 5 we
mentioned to ensure you have Python 3.8+ 64-bit installed. This is a good opportunity to double check: 
The Python executable referenced in the `home` variable assignment in the `pyvenv.cfg` file should in fact 
be this version of Python. Also be certain that your PATH environment variable is set properly to refer to this executable.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/25_1_VSCode_Python_virtual_environment_configuration.png" alt="drawing" width="800"/>



## 7 Code break

In the same location as `__init__.py` add a code file called `factoring_code.py` with these contents: 


```
def factor_integer(n):
    if n < 4: return [n]
    prime_factors, factor_candidate = [], 2
    while factor_candidate ** 2 <= n:                        # regime of possible factors
        if n % factor_candidate:                             # here n is not divisible
            if factor_candidate == 2: factor_candidate = 3   # increase candidate factor
            else:                     factor_candidate += 2  # ...not efficient!...
        else:
            n = n / factor_candidate                 # n *is* divisible by a factor
            prime_factors.append(factor_candidate)   
    if n > 1: prime_factors.append(int(n))           # options exhausted: a residual n > 1  
                                                     #   will be the (float) last factor
    return prime_factors
```

Modify the file `__init__.py` to read as follows. (Notice the leading period `.` in the import statement for the factoring code.)

```
import logging
import azure.functions as func
from .factoring_code import factor_integer

def main(req: func.HttpRequest) -> func.HttpResponse:

    logging.info('Python HTTP trigger function processed a request')
    n = req.params.get('n')
    if not n: reply_string = 'Results: No n parameter, no silk purse for you!'
    else:                                         # runs if 'n' was found (will be of type string)
        factor_list = factor_integer(int(n))      # gets a list of factors
        factor_string = ' * '.join([str(f) for f in factor_list])
        reply_string = 'Results: ' + str(n) + ' has ' + str(len(factor_list)) + ' factors, is = ' \
                       + factor_string + ' ... thanks for playing!'

    return func.HttpResponse(reply_string, status_code = 200)
```

In summary: We do not make any configuration changes to the default Azure Function. We did add an additional Python code file
and we imported its sole function into the main program. The main program was also modified to look for a parameter `n` 
passed in the HTTP trigger request. 


## 8 Test locally


Make sure the lower-central tabbed console is open (ctrl + j). Select the TERMINAL tab.
Use the **Run** menu or **F5** to debug the program. This will provide a test URL on `localhost`
as shown in the larger red rectangle below. Ctrl+click on this link. 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/27_1_VSCode_test_Azure_Function_localhost.png" alt="drawing" width="800"/>


This should open a browser tab where the "thanks for playing" message will be printed. 
As an integer was not provided, nothing was factored. To test the factor function append `?n=15` to the 
URL in the browser address bar. 


Notice the **Run / bug** icon in the activity bar displays a blue badge.
This indicates the Azure Function process is running locally.
Stop the Function, clear the badge.


## 9 Deploy to Azure


It remains to click the *Deploy to Azure* button and choose where the deployment goes. From the 
[activity bar](#prefatory-on-vscode) 
select the Azure
icon. As before: Hover on **FUNCTIONS** in the sidebar to illuminate the deployment / up-arrow icon. It is circled in white here:


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/29_1_VSCode_deploy_button.png" alt="drawing" width="400"/>


Note that while this Azure Function is deploying to the Azure cloud, the lower console directs your attention to the **OUTPUT** tab.  
Here, once the deployment is completed, the new Function is provided as a URL on *azurewebsites.net*. Ctrl + click and test as before
with the local version. 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/30_1_VSCode_successful_deployment_to_Azure.png" alt="drawing" width="400"/>


Here is an example test result:


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/30_2_VSCode_browser_test_Azure_Function.png" alt="drawing" width="800"/>


If this works as planned we have completed coding, testing, deploying and executing the Azure Function on the Azure cloud. 


## 10 Programmatic test


The last thing we want to do is to automate the Azure Function trigger. That is: Place the trigger action inside of a Client Python program.


Ensure that the Python `requests` library is installed in your environment. Run this simple program intended to get
the factorization of 144:


```
import requests
urlbase='https://azfn1.azurewebsites.net/api/azfn1'
print(requests.get(urlbase + '?n=144').text)
```

This completes the construction and testing of a simple serverless computation service. The first million
executions of this function on Azure are at no cost. The service will be stable and should "just work" for 
the foreseeable future which on the cloud is typically about three to five years.


Real implementations will tend to be more complex. They will particularly tend to involve connecting 
the serverless function with data resources. While this is 
beyond the scope of this tutorial, a vast array of instructional resources are 
available online. 

## Appendices

### Docker for the uninitiated

- [Windows installation](https://docs.docker.com/docker-for-windows/install/)
- Download the installer, run through the installation, start the Docker app (this takes some time)
- Run through the tutoria ("2 minutes")
    - Notice this tutorial places an active Windows Powershell in the right side of the application panel
    - Running the first two commands `docker run` and `docker cp` in this shell sets in motion:
        - git clone of the github docker-org `getting-started` repo (from `alpine/git:latest`)
        - this is into `~/getting-started`
    - Next is `docker build` from the clone directory: Builds the 101 tutorial. This takes a minute.
    - Next `docker run` runs this newly built docker container, mapping port 80 of the container to port 80 of the OS. 
    - Next `docker tag` and `docker push`; so now this image is registered under my docker user account

Let's back up from the "docker image at Docker Hub" part: I use `localhost/tutorial` to see the tutorial web app. 
That is, a running docker container is serving HTML to my browser when I hit that localhost URL. Great; there 
ought to follow some more illumination on the `docker do-something` syntax and the Docker Windows app. 

Turning to that app for a moment: The `alpine/git` repo features four action icons but only two are activated: 
Start and Delete. It's status is EXITED(0). The `docker-tutorial` Docker container is RUNNING on port 80 and 
fiveicons are active: Open in browser, CLI, Stop, Restart and Delete. 

Clicking on the **CLI** icon gets me a shell window with a `#` prompt. So this is the Part B of the Docker
container: Not only is it "running as a program" but it also "has a shell where I am root". Here is a point of
interest: The process list command `ps -ef` turns up one `master` process, quantity eight `worker process`, 
one `sh` shell and of course `ps -ef` itself. The master and eight workers are all under the `nginx` web server. 
So that is in short the smoking gun of "what this container is doing as an exectuable program" (in addition to
running a shell). 

Ok so there is a Docker image out there; and a Docker executable. Is this like a JVM? Going to the executable
from the image is ... well we did a build in the tutorial so I'm voting on the JVM idea. Anyway you get
something (where is it???) and you then run it and there is your textbook quote. 

### Understanding Docker in relation to WSL 2

From [this Docker Desktop page](https://docs.docker.com/docker-for-windows/wsl/) about the WSL 2 backend: 

> Windows Subsystem for Linux (WSL) 2 introduces a significant architectural change as it is a full Linux 
> kernel built by Microsoft, allowing Linux containers to run natively without emulation. With Docker Desktop 
> running on WSL 2, users can leverage Linux workspaces and avoid having to maintain both Linux and Windows 
> build scripts. In addition, WSL 2 provides improvements to file system sharing, boot time, and allows 
> access to some cool new features for Docker Desktop users.

> Docker Desktop uses the dynamic memory allocation feature in WSL 2 to greatly improve the resource 
> consumption. This means, Docker Desktop only uses the required amount of CPU and memory resources 
> it needs, while enabling CPU and memory-intensive tasks such as building a container to run much faster.

> Additionally, with WSL 2, the time required to start a Docker daemon after a cold start is 
> significantly faster. It takes less than 10 seconds to start the Docker daemon when compared 
> to almost a minute in the previous version of Docker Desktop.



