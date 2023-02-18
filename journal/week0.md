# Week 0 — Billing and Architecture
## Required Homework/Taksk
-------
### Install and Verify AWS CLI
*  Installed AWS CLI via Gitpod. I have it installed already. I checked to see if there is an update. It appeared that the latest version of AWS CLI is running on my system.

![update](./images/awscli%20install.png)

* Setup AWS CLI to use partial auto prompt mode
* The bash commands we are using are the same as the instructions in this link[AWS CLI Install Instructions](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

### Updated .gitpod.yml to include the following task.

![gitpod](./images/gitpod%20update.png)

### Create a new User and Generate AWS Credentials
* Go to [IAM Users Console](https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#/users) 
* create a new user
* Enable console access for the user
* Create a new Admin Group and apply AdministratorAccess
* Create the user and go find and click into the user
* Click on Security Credentials and Create Access Key, Choose AWS CLI Access
* Download the CSV with the credentials

![iam](./images/admin%20user.png)