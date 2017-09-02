# Part 3 - IAM Roles

In this part we are going to look at IAM rules. IAM controls how AWS services can interface with each other, by use of IAM rules.

We are going to create a simple database, and a Lambda function that attempts to write to it.

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
>>> client.describe_table(TableName='21301438_ddb_tbl')['Table']['TableStatus'] # Use your student number
'ACTIVE'
```

**Take a screenshot of the above command for the submission.**

## Setting up Lambda

Lambda functions allow you to run small pieces of code in AWS, generally in response to some event.  
In our case, we are going to add data to our dynamodb table.

### Creating a Lambda Function

We are going to create the lambda function in AWS, as it's quicker and easier than boto.  
Note, however, that all these steps could be done from boto.

1. Go to the lambda section in AWS: [https://ap-southeast-2.console.aws.amazon.com/lambda/home?region=ap-southeast-2\#/functions](https://ap-southeast-2.console.aws.amazon.com/lambda/home?region=ap-southeast-2#/functions)
2. Click _Create Function_ then _Author from Scratch_
3. In _Configure Triggers_, just click _Next_

Add the following in the _Configure Function_ page:

| Field | Value | Example |
| :--- | :--- | :--- |
| Name | You student Number + \_lambda | 21301438\_lambda |
| Description | blank |  |
| Runtime | Python3.6 | Python3.6 |
| Code | Leave as is |  |
| Handler | Leave as is | lambda\_function.lambda\_handler |
| Role | Chose an existing role |  |
| Existing Role | lambda\_basic\_execution |  |

Click _Next_ and _Create function_.

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

Click \_Save and test  
\_Ignore the \_Input test event \_dialog and click \_Save and test \_again.

You should see an error. Click 'Details' and **take a screenshot for the submission**. It should look like so \(but with your student number\):![](/assets/lambda_error.png)This error is because Lambda does not have IAM access to dynamodb.  
We can fix this by changing the execution roles:

Click the _Configuration _tab,_ Change Existing Role_ to _lambda\_dynamodbFull\_role_

This role gives your Lambda full access to dynamodb.  
Click _Save and test_. You should get a green tick now.

### Get our inserted value

Go back to your python interpreter:

```py
>>> import boto3
>>> dynamodb = boto3.resource('dynamodb')
>>> table = dynamodb.Table('yourstudentnumber_ddb_tbl')
>>> table.scan()
```

**Take a screenshot of the results of the above for the submission.**

## Cleanup:

1. **Delete the Lambda function **- click _Actions _-&gt; _Delete function_
2. **Delete the DynamoDB **- go to [https://ap-southeast-2.console.aws.amazon.com/dynamodb/home?region=ap-southeast-2\#tables](https://ap-southeast-2.console.aws.amazon.com/dynamodb/home?region=ap-southeast-2#tables)
3. Click \_Tables \_from the list on the left, and select your table
4. Click _Actions_ then _Delete table_
5. **Terminate your EC2 instance**_** **_**- **go to [https://ap-southeast-2.console.aws.amazon.com/ec2/v2/home?region=ap-southeast-2\#Instances](https://ap-southeast-2.console.aws.amazon.com/ec2/v2/home?region=ap-southeast-2#Instances), select your instance and click _Actions_ -&gt; _Instance State -&gt; Terminate_

#### [Next: Conclusion](/conclusion.md)



