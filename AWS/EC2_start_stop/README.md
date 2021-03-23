These are notes from following the procedure [here](https://aws.amazon.com/premiumsupport/knowledge-center/start-stop-lambda-cloudwatch/).
That is a comprehensive walk-through that creates both a Start and a Stop Lambda with corresponding daily CloudWatch alarms.
To begin with I will only do the **Stop** operation.

> ***WARNING:*** If you already have an EC2 running that contains your work you may wish to test this 
> procedural on a different machine to be sure it works as intended, does not Terminate the instance;
> then back up your work prior to running this Stop procedural on your 'real' EC2.


* Region: Oregon (nearest): Noting this is `us-west-2`. 
* I created the necessary policy in the JSON editor (under IAM), named it `startstop`, gave it tags
* The IAM role for my pending Lambda is also called `startstop`.
    * Notice the above *Policy* is in the *Role* but the *Role* is unassociated at this point.
* Created the lambda function: I'm following the 'stop' recipe only
    * Start it up again: Will be left as a manual task for now (kilroy)
        * (kilroy) it is more accurate to name things startinstances and stopinstances rather than startstop.,,
    * In the code source window: click on the left sidebar `lambda_function.py` to activate the editor for this file.

> **IMPORTANT:** Make sure to adjust both region and instance values to match your implementation


> **IMPORTANT:** The instance IDs *should* be imported from environment variables, making this code more secure.


* Do the import env vars (kilroy) for fully detailed walk-through that included the code
    * Timeout is under the Configuration tab (not the default Code tab).
    * Save the code and be sure to **Deploy** it

* Creating the CloudWatch alarm trigger
    * Name, description, choose "Schedule" and "Cron expression"
    * My cron format is `0  5  ?  *  3-7  *` which translates as "5AM Zulu every Tuesday through Saturday" (10AM PDT)
        * Days of the week are 1-7 SUN-SAT; but it uses less-than logic
        * The ? wildcard means use days of week, not days of month (3rd field)
    * After the alarm set: Return to the Lambda function and did a refresh: Now the CloudWatch alarm appears as a trigger
        * I could have but did not do this from within the Lambda Function page

This function would be much cooler if it also sent me an email that it ran. 
Here is the code:

```
import boto3
region = 'us-west-2'
instances = ['i-aaabbbcccdddeeeff']
ec2 = boto3.client('ec2', region_name=region)

def lambda_handler(event, context):
    print('start stopping instances')
    ec2.stop_instances(InstanceIds=instances)
    print('done stopping instances')
```
