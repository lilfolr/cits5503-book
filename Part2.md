# Part 2 - EC2 Security Groups

## Getting Started & Checking boto3 is installed

Open the python interpreter \(after activating your virtualenv from the [previous](/Part1.md) part.

```
(env)  $ python
```

Not run the following[^1]:

```py
>>> import boto3
>>> s3 = boto3.resource('s3')
>>> [bucket.name for bucket in s3.buckets.all()]
```

If setup correctly, you should see a list of the s3 buckets in our aws account.

## Security groups

A security group acts like a firewall for your ec2 instance, allowing certain packets through \[eg, 80 & 443 for http & https\], and blocking others \[3306 for MySQL\].

On the AWS console you can view security groups by going to the EC2 page, and clicking 'Security Groups' on the left bar.  
Or just click [here](https://ap-southeast-2.console.aws.amazon.com/ec2/v2/home?region=ap-southeast-2#SecurityGroups:sort=groupName) :\)

### Creating a Security Group

We are going to create a simple security group that only allows http \[80\] traffic.  
Open the python interpreter and type the following:

```py
>>> import boto3
>>> ec2 = boto3.resource('ec2')
>>> vpc = ec2.Vpc('vpc-66e03803')
>>> security_group = vpc.create_security_group(
    Description='21301438_Security_Group',            # Use your student number here
    GroupName='21301438_SG')                          # Likewise, just your student number
```

Note: `vpc-66e03803` is our default VPC ID, and can be found from [here](https://ap-southeast-2.console.aws.amazon.com/vpc/home?region=ap-southeast-2#vpcs:)

If you now run

```py
>>> [x for x in vpc.security_groups.filter(GroupNames=['21301438_SG'])]
[ec2.SecurityGroup(id='sg-1234567')]
```

You should get 1 result.



...  
allow icmp  
...

## Create an EC2 instance

...  
assign security group  
...  
check can ping  
...

## Change security group

...  
check can't ping  
...

[^1]: Note: If you have multiple aws profiles, set your uwa one as the default one for this lab \[in `~/.aws/credentials`\]

