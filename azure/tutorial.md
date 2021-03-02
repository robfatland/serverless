# Azure Function tutorial

## Overview

This file walks you through building a simple *serverless service* on the Azure cloud. This is called an *Azure Function*. 

## 1. Establish access to a Subscription including your authentication credentials

## 2. Portal work

<img src="https://github.com/robfatland/serverless/blob/main/azure/images/00_1_Azure_portal.png" alt="drawing" width="400"/>

<img src="https://github.com/robfatland/serverless/blob/main/azure/images/10_1_X.png" alt="drawing" width="400"/>
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/10_1_Create_a_resource.png" alt="drawing" width="400"/>
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/10_2_Choose_Azure_Function_App.png" alt="drawing" width="400"/>
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/10_3_Create_Function_App.png" alt="drawing" width="400"/>
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/10_4_Function_App_Wizard.png" alt="drawing" width="400"/>
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/12_1_Hosting_wizard.png" alt="drawing" width="400"/>
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/13_1_Monitoring_wizard.png" alt="drawing" width="400"/>
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/14_1_Tagging_wizard.png" alt="drawing" width="400"/>
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/15_1_Review_Create_wizard.png" alt="drawing" width="400"/>
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/16_1_Deployment_complete.png" alt="drawing" width="400"/>
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/17_1_FunctionApp_resources.png" alt="drawing" width="400"/>
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/18_1_FunctionApp_Monitoring.png" alt="drawing" width="400"/>
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/19_1_Resource_Group_on_portal.png" alt="drawing" width="400"/>
<img src="https://github.com/robfatland/serverless/blob/main/azure/images/19_2_FunctionApp_three_components.png" alt="drawing" width="400"/>

## 3. Code break

Here is the factoring code; save this in a file called `factor.py`:


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

Here is the main program code which goes into the file `__init__.py`. Notice the import statement for the above code has an unusual syntax.

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
