# Part 1 - Setting up boto
You will need python to use the boto API. We will be using python3, and boto3

## Installing python3 & pip3
You can either install python and pip on your computer, or ssh into an EC2 instance.  
For either choice, you can find a guide [here](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-python-3).[^1]


Verify the installation was successful by running the `python3` and `pip3` command:

```
$ python3 --version
Python 3.5.1

$ pip3 --version
pip 8.1.1
```

[^1]: Remember to `sudo apt-get update` if on an EC2 before installing.
## Create a virtual environment
Skip this step if you covered in the previous one.  
1. Type `pip3 install virtualenv`
2. Then `virtualenv env -p python3`
3. Finally `. env/bin/activate`. _Remember the dot at the front!_

## Install boto3
Your prompt should look something like this now:  
`(env) ubuntu@ip-172-1-1-1:~$`  
Showing you're in the virtual environment
1. Type `pip install boto3`

## TODO: CREATE CREDENTIALS - NEED PERMISSION
## Install & setup the AWS CLI
1. `sudo apt install awscli`
2. Type `aws configure` and follow the instruction, adding in your api key



