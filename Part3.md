# Part 3 - IAM Roles

In this part we are going to look at IAM rules. IAM controls how AWS services can interface with each other, by use of IAM rules.

We are going to create a simple database, and a lambda function that attempts to write to it.

## Setting up DynamoDB

DynamoDB is a NoSQL database. You don't need to know anything about databases to use it for this.

### Creating the table

As before, we will use boto3 for our commands:

```py
>>> import boto3
>>> client = boto3.client('dynamodb')
>>> client.create_table(TableName="21301438_ddb_tbl",                 # Use your student number
AttributeDefinitions=[{'AttributeName':"my_key", 'AttributeType':"S"}],
KeySchema=[{'AttributeName':"my_key", 'KeyType':"HASH"}],
ProvisionedThroughput={'ReadCapacityUnits': 1,'WriteCapacityUnits': 1}
)
```

It may take some time to create the table. You can check is status either on [AWS](https://ap-southeast-2.console.aws.amazon.com/dynamodb/home?region=ap-southeast-2#tables:), or by running the following:

Wait for it to be ACTIVE before continuing.

```
>>> client.describe_table(TableName='leighton_ddb_tbl')['Table']['TableStatus']
'ACTIVE'
```

**Take a screenshot of the above command**

## Setting up Lambda

Lambda functions allow you to run small pieces of code in aws, generally in response to some event.  
In our case, we are going to add data to our dynamodb table.

### Creating a Lambda Function

We are going to create the lambda function from in AWS, as it's quicker and easier than boto.  
Note, however, that all these steps could be done from boto.

Go to the lambda section in aws - [https://ap-southeast-2.console.aws.amazon.com/lambda/home?region=ap-southeast-2\#/functions](https://ap-southeast-2.console.aws.amazon.com/lambda/home?region=ap-southeast-2#/functions)

Click 'Create Function' --&gt; 'Author from Scratch'

In Configure Triggers, just click next

Add the following in the Configure Function page:

| Field | Value | Example |
| :--- | :--- | :--- |
| Name | You student Number + \_lambda | 21301438\_lambda |
| Description | blank |  |
| Runtime | Python3.6 | Python3.6 |
| Code | Leave as is |  |
| Handler | leave as is | lambda\_function.lambda\_handler |
| Role | Chose an existing role |  |
| Existing Role | lambda\_basic\_execution |  |

Click next and create function

### Running our lambda function

You should now see a page with a dialog to edit the code of your function:![](/assets/lambda_start.png)

Type the following into the above box to tell the lambda to access and write to your dynamo table:

```py
import boto3
from datetime import datetime

def lambda_handler(event, context):
    client = boto3.client('dynamodb')
    client.put_item(TableName='yourstudentnumber_ddb_tbl', Item={'my_key':{'S':"Hello "+str(datetime.now())}})
    return
```

Click Save and Test  
Ignore the Input test event dialog and click Save and test again.

You should see an error. Click 'Details' and **take a screenshot**. It should look like so \(but with your student number\):![](/assets/lambda_error.png)This error is because lambda does not have IAM access to dynamodb.  
We can fix this by changing the execution roles:

Click the Configuration tab change Existing role to `lambda_dynamodbFull_role`

This role gives your lambda full access to dynamodb  
click save and test. You should get a green tick now.

### Get our inserted value

Go back to your python interpreter:

```py
>>> import boto3
>>> dynamodb = boto3.resource('dynamodb')
>>> table = dynamodb.Table('yourstudentnumber_ddb_tbl')
>>> table.scan()
{'Items': [{'my_key': 'Hello your date'}], 'Count': 1, 'ScannedCount': 1, 'ResponseMetadata': {'RequestId': 'some id', 'HTTPStatusCode': 200, 'HTTPHeaders': {'server': 'Server', 'date': 'some date GMT', 'content-type': 'application/x-amz-json-1.0', 'content-length': '89', 'connection': 'keep-alive', 'x-amzn-requestid': 'some key', 'x-amz-crc32': '800716666'}, 'RetryAttempts': 0}}
```

**Take a screenshot of the above**

