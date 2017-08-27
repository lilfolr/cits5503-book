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

You should get 1 result. **Take note of the security group id.**

### Modify Security Group

We now have an empty security group[^2], so lets give it some rules.  
We want to allow HTTP traffic, from port 80.

```py
>>> security_group = ec2.SecurityGroup('sg-1234567')  # Use you security group id here
>>> security_group.authorize_ingress(
    CidrIp='0.0.0.0/0',
    FromPort=80, ToPort=80,
    IpProtocol='tcp')
{'ResponseMetadata': {'RequestId': 'some-id', 'HTTPStatusCode': 200, 'HTTPHeaders': {'content-type': 'text/xml;charset=UTF-8', 'transfer-encoding': 'chunked', 'vary': 'Accept-Encoding', 'date': 'some date GMT', 'server': 'AmazonEC2'}, 'RetryAttempts': 0}}
```

Now run ** and take a screenshot for submission** the following:

```py
>>> [x.ip_permissions for x in vpc.security_groups.filter(GroupNames=['21301438_SG'])]
[[{'FromPort': 80, 'IpProtocol': 'tcp', 'IpRanges': [{'CidrIp': '0.0.0.0/0'}], 'Ipv6Ranges': [], 'PrefixListIds': [], 'ToPort': 80, 'UserIdGroupPairs': []}]]
```

Here's a description of the paramaters for the authorize\_ingress command we just ran:

`CidrIp`: The range of IP addresses this rule applies to. any range with a `/0` at the end will allow all ip addresses. Google ip masks for more info  
`FromPort`&`ToPort`: The port range we're allowing. In this case it's just 1 port.  
`IpProtocol`: The protocol the rule applies to. This is either `tcp`; `udp` or `icmp`. Obviously if you pick ICMP you can't specify a port range, as ICMP is a layer 3 network protocol. That being said, you may need to specify one anyway to keep AWS happy.

### Assign Security Group

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

[^2]: An empty security group will block all incoming traffic, and allow all outgoing traffic

