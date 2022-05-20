EC2 Lambda function

Create EC2 Key Pair Navigate to EC2.
Enter a name e.g., "LambdaEC2keypair"
Save the private key file in a safe place.

Create a Lambda Function

Choose Author from scratch and use the following settings:

Name: CreateEC2
Runtime: Python 3.9
Role: Create a custom role
Expand Choose or create an execution role.

choose: Create a new role with basic Lambda permissions

Copy the execution role name that appears (it will be something like CreateEC2-role-). Paste it into a text file for use later in the lab.

Click Create function.

In IAM page Click Roles in the left-hand menu.

In the search box, type in the name of the role we just created.

Click Edit policy > JSON.

### Paste in the following policy:
###############################################
{
  "Version": "2012-10-17",
  "Statement": [{
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    },
    {
      "Action": [
        "ec2:RunInstances"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
###############################################

Click Review policy and then Save changes.

Now we have to polices:
* Cloudwatch Logs;
* EC2;

Back in the Lambda console, refresh the page.

On the CreateEC2 function page, scroll down to the Function code section.

Paste in the Python source code from this file on GitHub.

###############################################
import os
import boto3

AMI = os.environ['AMI']
INSTANCE_TYPE = os.environ['INSTANCE_TYPE']
KEY_NAME = os.environ['KEY_NAME']
SUBNET_ID = os.environ['SUBNET_ID']

ec2 = boto3.resource('ec2')


def lambda_handler(event, context):

    instance = ec2.create_instances(
        ImageId=AMI,
        InstanceType=INSTANCE_TYPE,
        KeyName=KEY_NAME,
        SubnetId=SUBNET_ID,
        MaxCount=1,
        MinCount=1
    )

    print("New instance created:", instance[0].id)
###############################################


Click Save.

Go to the Environment variables section.
Gonfiguration -> Environment variables
Set the following environment variables:

AMI
Key: AMI
Value: Open EC2 in a new browser tab, click Launch Instance, and copy and paste the ami value listed after Amazon Linux 2.

INSTANCE_TYPE
Key: INSTANCE_TYPE
Value: t2.micro

KEY_NAME
Key: KEY_NAME
Value: The name of the EC2 key pair you created earlier.

SUBNET_ID
Key: SUBNET_ID
Value: Navigate to VPC > Subnets, and copy and paste the ID of one of the public subnets in your VPC.
Save the Lambda function.

Test Lambda Function
Click Test.

Define an empty test event. Its contents can simply be {}.
Give it any name you like.
Click Create.
Click Test.
Navigate to EC2 > Instances, and observe that an EC2 instance is initializing.
