# Week 0 — Billing and Architecture

## Required Homework


### Created Conceptual/Architectural Diagram

- Created a basic architectural diagram to show the basic working of the project
- added frontend backend and other required resources for the architecture
- Its a conceptual diagram to show how things would work
- This diagram helps to get a general overview of the resources and stuff that we need to build out project

![Creating Architectural Diagram](assets/Conceptual%20Diagram.png)


### Created Logical Diagram in Lucid
- Logical diagram is the next version of the conceptual diagram
- we use it to allocate specific services provied by our cloud provider which are required for the project
- logical diagram contains specific and detailed names of resources required
- it helps us design the a plan for implementation and also helps us estimate working cost based on the services we are using
- Created a new blank template
- selected the AWS icons
- Dragged and arranged all the required icons to build a logical diagram
- Downloaded the Momento logo in SVG format directly from google and uploaded it into the shapes
- Adjusted all the resources and services to make the diagram smaller and easy to understand 
 ![Logical Diagram](assets/Cruddur%20Logical%20Diagram.png)
- [Cruddur Logical Diagram](https://lucid.app/lucidchart/e9da39b3-c83b-43d3-acd6-f61f5095d5e2/edit?viewport_loc=-378%2C112%2C2478%2C1250%2C0_0&invitationId=inv_317b7c8f-d40c-4acb-bd1c-170e823c98c8)

## Note - The white momento logo when downloaded as SVG makes the white background as PNG so its not visible properly in the Lucid Charts diagram , So i used the Green logo instead. You can then just change the background colour of the Image in lucid
![](assets/Logo%20diffrence%20SS.png)
![](assets/Logo%20Colour%20change.png)

## Creating IAM user
- Created a new IAM user ‘Neeraj’ from the root account using IAM
- created a new user group called ‘admin’ and gave Administrator access permissions to the group
- added the IAM user ‘Neeraj’ to the ‘admin’ user group so that the IAM user has admin access
- logged out of my Root account and logged back in with my IAM account using my IAM user name , alias and IAM password
- Went to IAM console and added MFA for the IAM account
![Creating IAM User](assets/Creating%20a%20IAM%20user%20and%20assigning%20admin%20group.png)

### IAM user Created 
![](assets/IAM%20user%20Created.png)

### MFA Enabled
![](assets/MFA%20enabled%20for%20Root%20and%20IAM.png)

### AccessKey Created
- From my root account I created a new access key using CLI


## Using CloudShell 
- Opened aws cloudshell and configured the auroprompt into the cloud shell and ran the AWS who am i check (sanity check)
![](assets/AWS%20Cloudshell%20Sanity%20check.png)

## Installing the CLI on Gitpod
- First open gitpod from your repo

- then enter the installation commands from the AWS CLI install webpage

- Follow the given instructions to successfully install the CLI onto GITPOD

- after successful installation confirmation is recieved

- the cli will not have your account credentials so you can use “aws configure” to configure the creds or you can use the export command to enter the accesskey and secret access key manually

- Then use the “aws sts get-caller-identity’ command to check if your credentials have been configured correctly

- when we reopen the gitpod the work we have done now will be gone so we use the given “tasks code to configure gitpod to run the same commands everytime we open that workspace repo

 

```yaml
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
vscode:
  extensions:
    - 42Crunch.vscode-openapi
```
![CLI installation Confirmation](assets/CLI%20installation%20confirmation.png)

- we so need to save the access keys in the gitpod directory so use the following codes to save the cred in the gitpod variables list


```yaml
gp env AWS_ACCESS_KEY_ID=""
gp env AWS_SECRET_ACCESS_KEY=""
gp env AWS_DEFAULT_REGION=us-east-1
```
- above we can enter our own creds


![Creds check Confirmation on Gitpod](assets/Creds%20Check%20Confirmation%20in%20GITPod.png)

- we also save out credentials in the Gitpod workspace using the following commands
![](assets/Saved%20the%20Account%20ID%20as%20a%20Gitpod%20Variable.png)

## Setting the budgets and billing alarms using AWS CLI

- Creating a Budget and Budget Alarm

- use the following code to create a json budgets file and changed the amount to 1$

```json
{
    "BudgetLimit": {
        "Amount": "100",
        "Unit": "USD"
    },
    "BudgetName": "Example Tag Budget",
    "BudgetType": "COST",
    "CostFilters": {
        "TagKeyValue": [
            "user:Key$value1",
            "user:Key$value2"
        ]
    },
    "CostTypes": {
        "IncludeCredit": true,
        "IncludeDiscount": true,
        "IncludeOtherSubscription": true,
        "IncludeRecurring": true,
        "IncludeRefund": true,
        "IncludeSubscription": true,
        "IncludeSupport": true,
        "IncludeTax": true,
        "IncludeUpfront": true,
        "UseBlended": false
    },
    "TimePeriod": {
        "Start": 1477958399,
        "End": 3706473600
    },
    "TimeUnit": "MONTHLY"
}
```
![Budget Creation using CLI](assets/Budget%20Creation%20using%20CLI%20completed.png)

- Then created a budget notification alarm using the following code

```json
[
    {
        "Notification": {
            "ComparisonOperator": "GREATER_THAN",
            "NotificationType": "ACTUAL",
            "Threshold": 80,
            "ThresholdType": "PERCENTAGE"
        },
        "Subscribers": [
            {
                "Address": "neerajtembare7@gmail.com",
                "SubscriptionType": "EMAIL"
            }
        ]
    }
]
```

- after creating the budget and budget notification json files, the following command is used to create a Cost and Usage budget

```json
aws budgets create-budget \
    --account-id $AWS_ACCOUNT_ID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json
```
 ![BUDGET CREATED](assets/Budget%20Creation%20using%20CLI%20completed.png)
 
 

## Note - Andrew had created two variables i.e. - ACCOUNT_ID  and AWS_ACCOUNT_ID in the video lecture and used the first variable to get account ID details,  so i had edit the code since I only created the AWS_Account_ID variable 

![Budget Verification on AWS Console](assets/Budget%20created%20In%20console%20using%20CLI%20and%20threshold%20set.png)

## Creating a Billing Alarm 

- for billing alarm we need to create a sns topic first
- The SNS topic is what will delivery us an alert when we get overbilled
- we use the below command to create a new sns topic with the name ‘billing-alarm’

```json
aws sns create-topic --name billing-alarm
```

- above command returns a TopicARN

- we now create a subscription to link the TopicARN to our email

```json
aws sns subscribe \
    --topic-arn="arn:aws:sns:us-east-1:390618280322:billing-alarm" \
    --protocol email \
    --notification-endpoint neerajtembare7@gmail.com
```

- after creating a sns topic we have to check out email and confirm the subscription for the sns topic
![SNS topic SUB confirmed](assets/SnS%20topic%20subscription%20Confirmed.png)
- now we create a new json file “alarm-config.json” to create the alarm

```json
{
    "AlarmName": "DailyEstimatedCharges",
    "AlarmDescription": "This alarm would be triggered if the daily estimated charges exceeds 1$",
    "ActionsEnabled": true,
    "AlarmActions": [
        "arn:aws:sns:us-east-1:390618280322:billing-alarm"
    ],
    "EvaluationPeriods": 1,
    "DatapointsToAlarm": 1,
    "Threshold": 1,
    "ComparisonOperator": "GreaterThanOrEqualToThreshold",
    "TreatMissingData": "breaching",
    "Metrics": [{
        "Id": "m1",
        "MetricStat": {
            "Metric": {
                "Namespace": "AWS/Billing",
                "MetricName": "EstimatedCharges",
                "Dimensions": [{
                    "Name": "Currency",
                    "Value": "USD"
                }]
            },
            "Period": 86400,
            "Stat": "Maximum"
        },
        "ReturnData": false
    },
    {
        "Id": "e1",
        "Expression": "IF(RATE(m1)>0,RATE(m1)*86400,0)",
        "Label": "DailyEstimatedCharges",
        "ReturnData": true
    }]
  }
```
![](assets/Billing%20Alarm%20Successfully%20created.png)
- our TopicArn is entered in the AlarmActions section so that it will trigger the alarm if the daily estimate charges reach over 1$
