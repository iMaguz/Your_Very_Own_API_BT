# Notes taken by MG

### Thu Mar 16 18:48:16 EDT 2023

---

1. Create a **DynamoDB table** called `jokes-api-table`
   - with partition key `joke_id` (String)
2. Create a **role** `joke-api-lambda-role`
   - give it `AmazonDynamoDBFullAccess` and
   - `AWSLambdaExecute` permissions.
3. Create **lambda function** `insert-joke-function`:
   - use **_Node.js 16.x_**
   - `joke-api-lambda-role` execution role.
   - add environment variables:
     - `table_name` = `jokes-api-table`.
     - `email` = xxx@xxx.com.
     - deploy the function.
     - create a test `insert-joke-function-test` and run it.
4. Create a **role** `joke-api-scheduler-role` and attach to it
   - inline policy `dynamo-cloudwatch-access-iam-policy`.
5. Create a new **CloudWatch EventBridge** schedule `get-new-joke`:
   - run every 5 minutes.
   - call `insert-joke-function`.
   - use `joke-api-scheduler-role` role.
6. Test the schedule.
   - After ~30 minutes, disable the schedule.
7. Create **lambda function** `list-jokes-function`:
   - use **_Node.js 16.x_**
   - `joke-api-lambda-role` execution role.
   - add environment variables:
     - `table_name` = `jokes-api-table`.
   - deploy the function.
   - create a test `list-jokes-function-test` and run it.
8. Create **lambda function** `get-random-joke-function`:
   - use **_Node.js 16.x_** and
   - `joke-api-lambda-role` execution role.
   - add environment variables:
     - `table_name` = `jokes-api-table`.
   - deploy the function.
   - create a test `get-random-joke-function-test` and run it.
9. Create **lambda function** `get-joke-by-id-function`:
   - use **_Node.js 16.x_** and
   - `joke-api-lambda-role` execution role.
   - add environment variables:
     - `table_name` = `jokes-api-table`.
   - deploy the function.
10. Build a new REST API in **API Gateway** `jokes-api`:
    - Choose "REST and "New API".
    - Description: `Simple API for jokes`
11. Add a new Resource `jokes`
    - "Actions" --> "Create Resource"
    - "Actions" --> "Create Method" --> GET
      - Lambda function --> `list-jokes-function`
12. Go to the root resource `/`
    - "Actions" --> "Create Method" --> GET
      - Lambda function --> `list-jokes-function`
13. Go to the `/jokes` resource
    - "Actions" --> "Create Resource" `joke_id`
      - Resource Path: /jokes/`{joke_id}`
14. Go to the `/jokes/{joke_id}` resource
    - "Actions" --> "Create Method" --> GET
      - Lambda function --> `get-joke-by-id-function`
      - Click on "Integration Request" --> "Mapping Templates"
        - "Request body passthrough" --> "When there are no templates defined (recommended)"
        - Add mapping template --> `application/json`
        - Scroll down and add input template.
        - Get a couple of joke_id values from the DynamoDB table and test it.
15. Go to the `/jokes` resource
    - "Actions" --> "Create Method" --> PUT
      - Lambda function --> `insert-joke-function`
16. Deploy API:
    - "Actions" --> "Deploy API"
    - "Deployment stage --> "New Stage"
    - "Stage name": `prod`
17. Copy Invoke URL: `https://rtvy41rizb.execute-api.eu-west-1.amazonaws.com/prod`
