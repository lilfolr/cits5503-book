# Part 2 - EC2 Security Groups

## Getting Started & Checking boto3 is installed

Open the python interpreter \(after activating your virtualenv from the [previous](/Part1.md) part.

```
(env)  $ python
```

Now run the following[^1]:

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
We want to allow traffic from port 8080.

```py
>>> security_group = ec2.SecurityGroup('sg-1234567')  # Use you security group id here
>>> security_group.authorize_ingress(
    CidrIp='0.0.0.0/0',
    FromPort=8080, ToPort=8080,
    IpProtocol='tcp')
{'ResponseMetadata': {'RequestId': 'some-id', 'HTTPStatusCode': 200, 'HTTPHeaders': {'content-type': 'text/xml;charset=UTF-8', 'transfer-encoding': 'chunked', 'vary': 'Accept-Encoding', 'date': 'some date GMT', 'server': 'AmazonEC2'}, 'RetryAttempts': 0}}
```

Now run** and take a screenshot for submission** the following:

```py
>>> [x.ip_permissions for x in vpc.security_groups.filter(GroupNames=['21301438_SG'])]
[[{'FromPort': 8080, 'IpProtocol': 'tcp', 'IpRanges': [{'CidrIp': '0.0.0.0/0'}], 'Ipv6Ranges': [], 'PrefixListIds': [], 'ToPort': 8080, 'UserIdGroupPairs': []}]]
```

Here's a description of the paramaters for the authorize\_ingress command we just ran:

`CidrIp`: The range of IP addresses this rule applies to. any range with a `/0` at the end will allow all ip addresses. Google ip masks for more info  
`FromPort`&`ToPort`: The port range we're allowing. In this case it's just 1 port.  
`IpProtocol`: The protocol the rule applies to. This is either `tcp`; `udp` or `icmp`. Obviously if you pick ICMP you can't specify a port range, as ICMP is a layer 3 network protocol. That being said, you may need to specify one anyway to keep AWS happy.

### Assign Security Group

We will now assign this security group to an ec2 instance.  
First, create an EC2 instance \[see lab 2 on how to do this\].  
Note that by default an EC2 gets assigned a Security Group allowing port 80; 22 and 443.

Lookup your instance ID in aws. It should be something  like `i-0e5129ea3ee1a6982`

Now we are going to attach the security group to our instance \[and detach the default ones\]

```py
>>> instance = list(ec2.instances.filter(InstanceIds=['i-0e5129ea3ee1a6982']))[0]
>>> instance.security_groups
[{'GroupName': 'launch-wizard-5', 'GroupId': 'sg-xxxxxx'}]  # This is the default group
>>> instance.modify_attribute(Groups=['sg-1234567'])
{'ResponseMetadata': {'RequestId': 'some id', 'HTTPStatusCode': 200, 'HTTPHeaders': {'content-type': 'text/xml;charset=UTF-8', 'transfer-encoding': 'chunked', 'vary': 'Accept-Encoding', 'date': 'some date GMT', 'server': 'AmazonEC2'}, 'RetryAttempts': 0}}
```

Note: you will now be unable to ssh into this instance \[as we have blocked port 22\]

On AWS, take not of the public DNS \(IPv4\) of your instance:![](/assets/DNS.png)

#### Showing port 8080 is open

Open a new terminal, and try ping the instance:

```bash
$ ping ec2-52-62-225-49.ap-southeast-2.compute.amazonaws.com
PING ec2-52-62-225-49.ap-southeast-2.compute.amazonaws.com (52.62.225.49): 56 data bytes
Request timeout for icmp_seq 0
Request timeout for icmp_seq 1
```

It should timeout, as we have blocked ICMP \[push ctrl+c to stop pinging\].

Now try the following:

```bash
$ nc -G 3 -v -z ec2-52-62-225-49.ap-southeast-2.compute.amazonaws.com 80
nc: connectx to ec2-52-62-225-49.ap-southeast-2.compute.amazonaws.com port 80 (tcp) failed: Operation timed out
$ nc -G 3 -v -z ec2-52-62-225-49.ap-southeast-2.compute.amazonaws.com 8080
nc: connectx to ec2-52-62-225-49.ap-southeast-2.compute.amazonaws.com port 8080 (tcp) failed: Connection refused
```

The netcat \[nc\] script tries to connect to to given port. `-v` is for verbose output. `-G` is a timeout.  
We can see that port 80 is closed, as nc never connects, but port 8080 is open.  
As we have no webserver listening to the port, the connection is refused.

**Screenshot the ping & netcat above**

## Change security group

Lets assume we now want to allow ping packets.  
We need to allow the ICMP protocol in our security group.

Note, this is back in the python interpreter

```py
>>> security_group = ec2.SecurityGroup('sg-1234567')
>>> security_group.authorize_ingress(
    CidrIp='0.0.0.0/0',
    FromPort=-1, ToPort=-1,
    IpProtocol='icmp')
```

We use -1 as the Ports as ICMP has no port.

Go back to your other terminal and try ping the instance again:

```
$ ping ec2-52-62-225-49.ap-southeast-2.compute.amazonaws.com
PING ec2-52-62-225-49.ap-southeast-2.compute.amazonaws.com (52.62.225.49): 56 data bytes
64 bytes from 52.62.225.49: icmp_seq=0 ttl=49 time=68.548 ms
64 bytes from 52.62.225.49: icmp_seq=1 ttl=49 time=66.349 ms
```

You should now get a response. **Take a screenshot of this**

## Cleaning up

Log on to AWS and terminate your EC2 instance

Then go to the security groups page, and delete your group.  
Note, you will need to wait for you instance to terminate before deleting the group

If you're up for a challenge, try use boto to delete your resources :\)

#### [Next: IAM Roles](/Part3.md)

[^1]: Note: If you have multiple aws profiles, set your uwa one as the default one for this lab \[in `~/.aws/credentials`\]

[^2]: An empty security group will block all incoming traffic, and allow all outgoing traffic

