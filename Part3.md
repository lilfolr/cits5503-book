# Part 3 - IAM Roles

In this part we are going to look at IAM rules. IAM controls how AWS services can interface with each other, by use of IAM rules.

We are going to create a simple database, and a lambda function that attempts to write to it.

## Setting up DynamoDB

DynamoDB is a NoSQL database. You don't need to know anything about databases to use it for this.

### Creating the table

As before, we will use boto3 for all our commands:

```py
>>> import boto3
>>> client = boto3.client('dynamodb')
>>> client.create_table(TableName="leighton_ddb_tbl",                 # Use your student number
AttributeDefinitions=[{'AttributeName':"my_key", 'AttributeType':"S"}],
KeySchema=[{'AttributeName':"my_key", 'KeyType':"HASH"}],
ProvisionedThroughput={'ReadCapacityUnits': 1,'WriteCapacityUnits': 1}
)
```

## Create Lambda

Create lambda function to add data to dynamodb table  
...  
use default role  
...  
should fail

## Assign dynamo role to lambda

...  
should pass  
...

