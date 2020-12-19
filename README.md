# AWS Hermes

Hermes is an AWS Lambda function and its proper IAM configurations to make 'Cron as a Service' magic happens.

## Global Idea

The main idea of this project is to be able to perform administrator and repetitive tasks in your entire infrestructure via tags. With this Lambda function you could, for example, shut down a development environment at 6PM on business days, writing a tag named "stopTime" with the value "0 18 * * 1-5" on every instance involved.

This project is its very early days, there are a lot of work to do yet :)

## Usage

You must write a proper tag in resources in order to perform some administrator task, such as stop or start an instance, generate snapshots from EBSs, etc.

The name of the tag is the action and the value is a cron expression, including wildcards, numbers, ranges and enumerations. Some examples are:

Tag Name | Tag Value | Description
---- | ---- | ---
startInstance | * 9 * * 1-5 | Business days at 9AM, start my instances
stopInstance | * 18 * * * | My instances must be stopped at 6PM
createSnapshot | * 12 7,14,21 * * | At 12 AM, the days 7, 14 and 21 of every month, create a snapshot

## Set up a new Lambda Function.

You need to do some manual steps before start.

- Access to **Lambda** service in your AWS console.
- Click on **Create a Lambda function**.
- Select **lambda-canary** template. This template say that you are going to use python as programming languaje, and you are also using Lambda as a **scheduling event**.
- Choose a name for your scheduled event.
- Write a [relevant] description.
- Select the time which your function will be executed (1 hour is enough for most of the cases).
- Click Next.
- Select a Name for your function. Something like **Hermes**, to be original :)
- Write a [relevant] description.
- The Runtime must be **python2.7**
- Copy&Paste the code in **hermes.py** in the textarea below.
- Create a role for this lambda function with two policies attached: one with **hermesEC2.json** permissions and another one with **hermesEBS.json**. Associate them to lambda function.
- Default values for the advanced settings should fit. Maybe increment timeout to 30 secs...

### EC2

Tag your EC2 instances with this tags:

- **startInstance**: Start the instance.
- **stopInstance**: Stop the instance.
- **createAmi**: Create an AMI based on the instance
- and more to come... for example, rebootTime, terminateTime or createAMITime.

### EBS

Current supported tags for EBS Volumes are:

- **createSnapshot**: Generates a snapshot of specified EBS Volume.

### AMI

Current supported tags for AMIs are:

- **deleteAmi**: Delete the specified AMI

### Snapshot

Current supported tags for Snapshots are:

- **deleteSnapshot**: Delete the specified Snapshot

## Limitations

- Currently it only supports EC2 instances, AMIs and EBS resources. More services coming.
- The HOUR you define in cron, is UTC (usually 1 hour less than Madrid time, but be careful with Daylight Saving Time!).
- When you configure the Lambda function, you specify time interval which Lambda function runs. You must match the "current" time (the exact time where Lambda function runs) with the exact time you define in your cron expression, so don't be very strict with minutes. Wildcards and ranges are recommended.
- The resources must be created out of Lambda before using this function.
