These are notes from following the procedure [here](https://aws.amazon.com/premiumsupport/knowledge-center/start-stop-lambda-cloudwatch/).
That is a comprehensive walk-through that creates both a Start and a Stop Lambda with corresponding daily CloudWatch alarms. 

* If you already have an EC2 running that contains your work...
    * You may wish to test this procedural on a different machine first to be sure that it works as intended...
        * ...and does not Terminate the instance ...and back up your work ...prior to running this Stop on your working EC2
* Region: Oregon (nearest): Check noting this is `us-west-2`. 
* No drama: I created the necessary policy in the JSON editor (under IAM), named it `startstop`, gave it a couple of tags
* The IAM role for my pending Lambda is also called `startstop`.
    * Notice the above *Policy* has landed in the *Role* but the *Role* is unassociated at this point.
* Creating the lambda function: I'm following the 'stop' recipe 
    * Start it up again: Will be left as a manual task for now (kilroy)
        * Since the method for starting is given notice (kilroy) it is more accurate to name things start and stop rather than startstop.,,
    * In the code source window: click on the left sidebar `lambda_function.py` to activate the editor for this file.
    * **IMPORTANT:** Make sure to adjust both region and instance values to match your implementation
    * **IMPORTANT:** The instance IDs *could* be imported from environment variables, making this code more secure.
        * I would do this for example if this was to be a fully detailed walk-through that included the code
    * Timeout is under the Configuration tab (not the default Code tab).
    * Deploy or "Publish"
* Creating the CloudWatch alarm trigger
    * As I'm doing this quickly I just did "every 10 hours" rather than cron format
    * After the alarm was set up I returned to the Lambda function and did a refresh: Now the CloudWatch alarm appears as a trigger
        * In other words: I did not have to do this from within the Lambda Function configuration page; but could have


```
import json
import boto3
region = 'us-west-2'
instances = ['i-aaabbbcccdddeeeff']
ec2 = boto3.client('ec2', region_name=region)

def lambda_handler(event, context):
    print('start stopping instances')
    ec2.stop_instances(InstanceIds=instances)
    print('done stopping instances')
```
