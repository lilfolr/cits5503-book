# Part 1 - Setting up boto

You will need python to use the boto API. We will be using python3, and boto3.

## Installing python3 & pip3

We'll be using a new EC2 instance, similar to that set up in Lab 1. The steps are outlined below - if you need more details, refer back to Lab 1.

1. Log into the AWS Console here: [https://cits5503.signin.aws.amazon.com/console](https://cits5503.signin.aws.amazon.com/console)
2. Select the EC2 Dashboard
3. Select the region \(top right of the screen\) to be Sydney
4. Click on Instances then Launch Instance
5. Select _Ubuntu Server 16.04 LTS \(HVM\), SSD Volume Type - ami-e2021d81_ as the OS type
6. Select _General Purpose t2.micro_ as the instance type
7. Click _Review and Launch \_then \_Edit security groups_
8. Click _Select an existing security group_. Here you should be able to find your security group from Lab 1.
9. Click _Review and Launch, _then_ Launch_
10. Select _Choose an existing key pair_ and find your student number in the list, then tick the acknowledgement box. 
    \(Note: you must remember where you saved your _\[student number\].pem_ file. If you don't follow the Lab 1 instructions to generate a new key pair\)
11. Click _Launch Instances_

Now ssh into your EC2 instance, following the Lab 1 instructions for your OS.

Then type `sudo apt-get update` then install by following the guide [here](https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-local-programming-environment-on-ubuntu-16-04).

Verify the installation was successful by running the `python3` and `pip3` command:

```
$ python3 --version
Python 3.5.1

$ pip3 --version
pip 8.1.1
```

## Create a virtual environment

1. Type `pip3 install virtualenv`  
2. Then `virtualenv env -p python3`  
3. Finally `. env/bin/activate`
   _Remember the dot at the front!_

## Install boto3

Your prompt should look something like this now:  
`(env) ubuntu@ip-172-1-1-1:~$`  
This shows that you are in the virtual environment  
Next, type `pip install boto3`

## Create AWS Access Key

1. Log on to AWS and go to the IAM app: [https://console.aws.amazon.com/iam/home?region=ap-southeast-2\#/home](https://console.aws.amazon.com/iam/home?region=ap-southeast-2#/home) 
2. Click the _Users_ tab from the list on the left and search for your student number
3. Click your student number and go to the _Security Credentials_ tab
4. Click the _Create access key_ button.
5. Download the csv.

_**NOTE: These credentials allow full access to your AWS account. They are equivalent to having your username and password... do NOT:**_

* _Upload them to ANY website_
* _Email them to anyone_
* _Put them in any source control \[git/mercurial\]_[^2][^3]

## Install & setup the AWS CLI

1. Back in your virtual environment, type`sudo apt install awscli -y`
2. Type `aws configure` and follow the instructions, adding in your AWS Access Key ID from the csv you downloaded.
   1. For default region, put _ap-southeast-2 _
   2. For default output, put _json_ 

#### [Next: EC2 Security Groups](/Part2.md)

[^2]: There are automated scripts that look on github for AWS credentials accidentally uploaded.

[^3]: Remember, if you commit your keys to git deleting them won't help. You will need to delete the repository or use something like [https://github.com/IBM/BluePic/wiki/Using-BFG-Repo-Cleaner-tool-to-remove-sensitive-files-from-your-git-repo](https://github.com/IBM/BluePic/wiki/Using-BFG-Repo-Cleaner-tool-to-remove-sensitive-files-from-your-git-repo) 

