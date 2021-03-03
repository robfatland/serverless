# Azure Function walkthrough

## Overview

This file walks you through building a simple *serverless service* on the Azure cloud. This is called an *Azure Function*. 

## 1. Establish access to a Subscription including your authentication credentials

## 2. Portal work

On logging in you have a browser view of the Azure portal; upper left corner looking something like this: 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/00_1_Azure_portal.png" alt="drawing" width="400"/>


'Resource' is the generic term for some item you'd like to allocate and use. 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/10_1_Create_a_resource.png" alt="drawing" width="200"/>


Type 'function' in the search window to help select the resource you want: An Azure Function App. 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/10_2_Choose_Azure_Function_App.png" alt="drawing" width="200"/>


Click on the **Create** button; so we can next proceed to a series of screen captures of the wizard-guided process.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/10_3_Create_Function_App.png" alt="drawing" width="200"/>


The **Basics** tab of the wizard defines some details -- including the name -- of the Azure Function. Defaults are fine but be
sure to check **Code**, and choose **Python** and **3.8** as shown. The region is ideally geographically close to you; this 
helps keep network access fast for you and otherwise makes little difference. 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/10_4_Function_App_Wizard.png" alt="drawing" width="700"/>


The **Hosting** tab should resemble the one shown below. Notice that a Storage Account is created by default and associated
with the Function App. The last box at the bottom indicates we are building a serverless function.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/12_1_Hosting_wizard.png" alt="drawing" width="700"/>


In the **Monitoring** tab you can choose to enable Application Insights. (This could also be done later.) This will associate tracking
resources with the Azure Function. These are helpful tools for understanding behavior and debugging problems.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/13_1_Monitoring_wizard.png" alt="drawing" width="700"/>


The **Tagging** tab allows you to annotate this Azure Function with key-value pairs. This is extremely valuable -- when one
takes the time to do it -- upon trying to remember what this Azure Function does after not thinking about it for four months. 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/14_1_Tagging_wizard.png" alt="drawing" width="700"/>


The final **Review / Create** tab allows you to launch the creation process. 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/15_1_Review_Create_wizard.png" alt="drawing" width="500"/>


If all goes well you arrive at a confirmation message.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/16_1_Deployment_complete.png" alt="drawing" width="600"/>


Now let's take stock of the Azure Function. This is a long (scrollable) list of its attributes or facets.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/17_1_FunctionApp_resources.png" alt="drawing" width="500"/>


Far down on that list is a section on Monitoring. Again this is where one would begin for careful debugging.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/18_1_FunctionApp_Monitoring.png" alt="drawing" width="400"/>


Again here is the resource group associated with this Azure Function.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/19_1_Resource_Group_on_portal.png" alt="drawing" width="400"/>


Here are the three resources in that resource group: The Azure Function, its associated Application Insights resource for monitoring, 
and a dedicated storage account.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/19_2_FunctionApp_three_components.png" alt="drawing" width="800"/>


This concludes the creation of the Azure Function using the portal. The idea was to illustrate the basic nature of the 
interaction.



## 3. Aside on templates


In the portal: **Generate template** produces JSON that can be "run" to recreate what was done manually. 


## 4. Switching to Visual Studio Code (VSCode)


We now install VSCode on a local computer or log in to a container environment where it has already been installed. 


## 5. Installing Azure extensions in VSCode


In what follows we need the VSCode *Azure Core Tools* extension and the *Azure Functions Core Tools* extension.  



## 6. Creating an Azure Function in FSCode


With VSCode running use ctrl + j to bring up a tabbed console in the lower part of the IDE. This acts as a kind of control
center while the pane above it acts as a file editor. 


To engage with the Azure cloud and Azure Functions using VSCode: Click the A-like or tent-like Azure icon in the left sidebar. 
It looks like this: 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/22_1_VSCode_Azure_tent_icon.png" alt="drawing" width="80"/>



For the first part of the VSCode process we do not connect with Azure. Rather we build and test an Azure Function locally, on our
own computer. To start this process: First select the Azure context as described above, then hover your cursor over **FUNCTIONS**
in the menu strip. This is also on the left side of the IDE, adjacent to the symbol strip at the far left. This hover action 
should bring up a *Create new project...* icon, a folder with a lightning bolt that looks like this:


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/22_2_VSCode_new_project_folder_icon.png" alt="drawing" width="80"/>


Create the host folder and make the following choices to configure the project: 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/23_1_VSCode_config_wizard.png" alt="drawing" width="600"/>


This will configure an environment to match these choices and create the project; takes a couple minutes.


Activate the explorer by clicking the double page icon at the upper left. You should see an item corresponding to your new project. Expand this view.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/24_1_VSCode_Azure_Function_file_explorer.png" alt="drawing" width="200"/>


The `requirements.txt` file describes packages that are installed in a Python virtual environment for the Azure Function to run.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/24_2_VSCode_requirements_dot_txt.png" alt="drawing" width="800"/>


Related: The file `pyvenv.cfg` describes the local Python executable path and version.


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/25_1_VSCode_Python_virtual_environment_configuration.png" alt="drawing" width="800"/>



## 7. Code break

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


## 8. Test locally


Make sure the lower-central tabbed console is open (ctrl + j). Select the TERMINAL tab.
Use the **Run** menu or **F5** to debug the program. This will provide a test URL on `localhost`
as shown in the larger red rectangle below. Ctrl+click on this link. 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/27_1_VSCode_test_Azure_Function_localhost.png" alt="drawing" width="800"/>


This should open a browser tab where the "thanks for playing" message will be printed. 
As an integer was not provided, nothing was factored. To test the factor function append `?n=15` to the 
URL in the browser address bar. 


While this Function is running locally the **Run + small bug** icon in the left-side context bar will display a blue badge.
Stop the Function to dismiss.


## 9. Deploy to Azure


It remains to click the *Deploy to Azure* button and choose where the deployment goes. From the left sidebar select the Azure
icon. As before: Hover on **FUNCTIONS** to see the deploy up-arrow icon. It is circled in white here:


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/29_1_VSCode_deploy_button.png" alt="drawing" width="400"/>


Note that while this Azure Function is deploying to the Azure cloud, the lower console directs your attention to the **OUTPUT** tab.  
Here, once the deployment is completed, the new Function is provided as a URL on *azurewebsites.net*. Ctrl + click and test as before
with the local version. 


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/30_1_VSCode_successful_deployment_to_Azure.png" alt="drawing" width="400"/>


Here is an example test result:


<img src="https://github.com/robfatland/serverless/blob/main/azure/images/30_2_VSCode_browser_test_Azure_Function.png" alt="drawing" width="800"/>


## 10. Programmatic test


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
