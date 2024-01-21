# RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB
RESTful Microservices with AWS Lambda, API Gateway and DynamoDB

## Creating IAM Policy
1.	On the **Management Console**, search for **IAM** then proceed to **Policies**.
2.	Click **Create Policy**

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/0f556641-8558-4673-a766-0ae5d50e98a0)

3.	Inside the create policy click for JSON then copy the code below. This will be the policy to be attached on the IAM Role for the Lambda function.

```
{
"Version": "2012-10-17",
"Statement": [
{
  "Sid": "Stmt1428341300017",
  "Action": [
    "dynamodb:DeleteItem",
    "dynamodb:GetItem",
    "dynamodb:PutItem",
    "dynamodb:Query",
    "dynamodb:Scan",
    "dynamodb:UpdateItem"
  ],
  "Effect": "Allow",
  "Resource": "*"
},
{
  "Sid": "",
  "Resource": "*",
  "Action": [
    "logs:CreateLogGroup",
    "logs:CreateLogStream",
    "logs:PutLogEvents"
  ],
  "Effect": "Allow"
}
]
}
```

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/95227bed-5580-4c0e-b326-c9238752f38a)

4.	Click Next then enter the Policy Name ```lambda-api-gateway-dynamodb```
5.	Hit **Create Policy**

## Attaching IAM Policy to an IAM Role
1.	In the IAM console, click for Roles in the left side of the screen then **Create Role**

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/91d16ec5-e8f8-4234-a30d-15096939bb90)

2.	Under the Select trusted entity, choose **AWS Service** for Trusted entity type then **Lambda** for Service or use case. Click next afterwards.
3.	In adding permissions, search up for the policy we created a while ago. Check it out then proceed to **Next**

  ![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/d8bee63c-a9af-4189-9be6-816505cde9a7)

4.	Name the Role into ```lambda-api-gatway-dynamodb-role``` then **Create Role**.

We now have the role for our Lambda function. This will create an action permissions for the function to the DynamoDB tables.

## AWS Lambda
1.	Open AWS Lambda then **Create a Function**
2.	Name the function as ```LambdaFunctionOverAPI``` then select the Runtime to Python 3.12
3.	For Permissions choose Change default execution role then click **Use an existing role** on Execution role
4.	Under existing role, choose the role that we created earlier, ```lambda-api-gateway-dynamodb-role```
5.	Then Click **Create Function**
6.	Go to **Code Source** tab then copy the code below.

```
from __future__ import print_function

import boto3
import json

print('Loading function')


def lambda_handler(event, context):
  #Provide an event that contains the following keys:
  #payload: a parameter to pass to the operation being performed

    #print("Received event: " + json.dumps(event, indent=2))

    operation = event['operation']

#tableName: required for operations that interact with DynamoDB
    if 'tableName' in event:
        dynamo = boto3.resource('dynamodb').Table(event['tableName'])

#operation: one of the operations in the operations dict below
    operations = {
        'create': lambda x: dynamo.put_item(**x),
        'read': lambda x: dynamo.get_item(**x),
        'update': lambda x: dynamo.update_item(**x),
        'delete': lambda x: dynamo.delete_item(**x),
        'list': lambda x: dynamo.scan(**x),
        'echo': lambda x: x,
        'ping': lambda x: 'pong'
    }

    if operation in operations:
        return operations[operation](event.get('payload'))
    else:
        raise ValueError('Unrecognized operation "{}"'.format(operation))
```

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/fd04f398-3849-40b0-a718-65cda6b1e54e)

10.	Click **Test**. You should see something like this on the **Execution results**. You should a have a payload test JSON below.

```
{
    "operation": "echo",
    "payload": {
        "somekey1": "somevalue1",
        "somekey2": "somevalue2"
    }
}
```

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/ec33fe41-eb08-4d44-84c7-645f43e148c9)

11.	This means our Lambda function is ready. We’ll now proceed on creating tables in DynamoDB.

## Creating Table in Amazon DynamoDB
1.	In Management Console, search for DynamoDB then click for **Create Tables**
2.	Name the table to ```lambda-apigateway``` then put ```id``` for the Partition Key and leave all settings in default. **Click Create** table afterwards.

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/91cfa8e0-23e9-4ae9-851e-b7b26eb57142)

3.	We now have created our DynamoDB tables. Now we will create the API for our via API Gateway.

## Creating our API via API Gateway
1.	Open API Gateway
2.	Create new API click **BUILD** on REST API. Then name your API to ```DynamoDBOperations```
3.	Create Resource then name it ```dynamodbmanager```
4.	Click on ```/dynamodbmanager``` then click **Create Method** then choose **POST** on Method Type, Integration Type is **Lambda Function** and choose the function we created on Lambda beside ```us-west-2```. After that click **Create Method**. The image below is for reference.

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/e28a2a35-e20e-4538-b284-2e49dd1365a2)

5.	Let us deploy our API by clicking **Deploy API**

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/2f8c2cae-af2b-4286-9af8-c4e666984565)

6.	Choose ```*New Stage*``` then name it ```Prod```.
7.	After deploying, copy the Invoke URL of our stage under the POST method

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/1127b883-3909-47a3-ab15-341886be5867)

8.	Save this URL as we will use this on POSTMAN

## POSTMAN
1.	Open up POSTMAN then **paste the URL** on the search bar with **POST** method beside.
2.	Go to **Body** then **raw** and copy and paste this JSON. You will see here the key and values payload that will be stored in our DynamoDB table which are the “id” and the “number”.

```
{
    "operation": "create",
    "tableName": "lambda-apigateway",
    "payload": {
        "Item": {
            "id": "1234ABCD",
            "number": 5
        }
    }
}
```

4.	Click **Send**

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/af09d2ec-112c-407c-bf17-5ffa2e3b7058)

4.	You should get a response something like this. This means we have successfully sent a payload to our DynamoDB table

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/cc51857e-ac50-41aa-8c93-f6184ff55572)

5.	Let us check our inserted items in our table using POSTMAN by changing the raw JSON file **Body** into this:

```
{
    "operation": "list",
    "tableName": "lambda-apigateway",
    "payload": {
    }
}
```

6.	Then hit **Send** button again
7.	You should be able to view the list in our DynamoDB table something like this

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/ebf76c17-840e-40b3-aa65-da222fd294ff)

8.	Let us also check it on DynamoDB

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/30704a3a-aaaf-4ddf-979a-adbcff6b7a65)

9.	There you go! You have successfully created a serverless API using API Gateway, Lambda, and DynamoDB!

## Cleaning Up our Works
1.	First is to delete our DynamoDB table by selecting our table then hit the Delete button

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/eedbd7c7-9104-4fbd-95ae-330686871428)

2.	Delete our API on API Gateway

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/6d900bf8-756c-4640-a106-45ce0e580dd8)

3.	Delete our function in AWS Lambda


![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/35cce86b-97df-4111-803d-be84f08d4320)

