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

## Create a security group



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

