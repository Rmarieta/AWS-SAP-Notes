# AWS Step Functions

- Step Functions address limitations of Lambda product
- AWS Step Functions = name of the service **VS** State machine = execution of the workflow itself created in the Step Functions service
- A Lambda function has an execution time of maximum 15 minutes
- Lambda functions can be chained together, but it is considered to be an anti-pattern and it can get messy. Lambda runtime environments are stateless
- States can do things, can decide things, can take in data, modify data and output data
- Maximum duration for state machine execution is **1 year**
- Step Functions can represent 2 different type of workflow:
  - Standard workflow: **default**, and it has a 1 year execution limit
  - Express workflow: designed for high volume event processing (IoT, streaming data processing and transformations), which can run **up to 5 min**
- State machines can be initiated with API Gateway, IoT Rules, EventBridge, Lambda or even manually
- State machines for Step Functions can be created with Amazon State Language (ASL) JSON templates
- IAM Roles used for permissions

## States

- Type of states available for a state machine:
  - `SUCCEED` and `FAIL`
  - `WAIT`: will wait for a certain period of time or until a date
  - `CHOICE`: allows a state machine to take a different path depending on an input
  - `PARALLEL`: allows to create parallel branches in a state machine
  - `MAP`: expects a list of things, for each it will perform a specific task
  - `TASK`: represents a single unit of work performed by a state machine. It can be integrated with: Lambda, Batch, DynamoDB, ECS, SNS, SQS, Glue, SageMaker, EMR, other Step Functions, etc.
