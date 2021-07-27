# Lambda-function-template
This repository describes the step i took while trying to create a lambda function template in cf

Step 1
I defined an IAM role for my lambda function with the following:

````
Resources:
  LambdaRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: LambdaRole
      AssumeRolePolicyDocument:
        Statement:
          - Effect: Allow
            Principal:
              Service: lambda.amazonaws.com
            Action: sts:AssumeRole
`````
