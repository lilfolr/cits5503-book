# Part 1 - Setting up boto

You will need python to use the boto API. We will be using python3, and boto3.

## Installing python3 & pip3

We'll be using a new EC2 instance, similar to that set up in Lab 1. 

Follow the Lab 1 instructions to set up the new instance - noting that you can re-use your security group and key pair.

Once you have accessed your instance via ssh, type `sudo apt-get update` then install by following the guide [here](https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-local-programming-environment-on-ubuntu-16-04).

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
3. Finally `. env/bin/activate`. _Remember the dot at the front!_

## Install boto3

Your prompt should look something like this now:  
`(env) ubuntu@ip-172-1-1-1:~$`  
This shows that you are in the virtual environment  
Next, type `pip install boto3`

## Create AWS Access Key

1. Log on to AWS and go to the IAM app \[[https://console.aws.amazon.com/iam/home?region=ap-southeast-2\#/users\](https://console.aws.amazon.com/iam/home?region=ap-southeast-2#/users\)\]
2. Click the Users tab and search for your student number
3. Click your student number and go to the Security Credentials Tab
4. Click the Create access key button.
5. Download the csv.

**NOTE: These credentials allow full access to your AWS account. They are equivalent to having your username and password  
DO NOT:**

* Upload them to ANY website
* Email them to anyone
* Put them in any source control \[git/mercurial\][^2][^3]

## Install & setup the AWS CLI

1. `sudo apt install awscli -y`
2. Type `aws configure` and follow the instruction, adding in your api key \[can be found in the csv you downloaded\]
   1. For default region, put 'ap-southeast-2' \(no quotes\)
   2. For default output, put 'json' \(no quotes\)

#### [Next: EC2 Security Groups](/Part2.md)



[^2]: There are automated scripts that look on github for AWS credentials accidentally uploaded.

[^3]: Remember, if you commit your keys to git deleting them won't help. You will need to delete the repository or use something like [https://github.com/IBM/BluePic/wiki/Using-BFG-Repo-Cleaner-tool-to-remove-sensitive-files-from-your-git-repo](https://github.com/IBM/BluePic/wiki/Using-BFG-Repo-Cleaner-tool-to-remove-sensitive-files-from-your-git-repo) 

