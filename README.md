# RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB
RESTful Microservices with AWS Lambda, API Gateway and DynamoDB

## Creating IAM Policy
1.	On the **Management Console**, search for **IAM** then proceed to **Policies**.
2.	Click **Create Policy**

![image](https://github.com/didin012/RESTful-Microservices-with-AWS-Lambda-API-Gateway-and-DynamoDB/assets/104528282/0f556641-8558-4673-a766-0ae5d50e98a0)

3.	Inside the create policy click for JSON then copy the code below. This will be the policy to be attached on the IAM Role for the Lambda function.

CODE HERE

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
