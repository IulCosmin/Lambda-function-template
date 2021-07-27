# Lambda-function-template
This repository describes the step i took while trying to create a lambda function template in cf

**Step 1**
- I defined an IAM role for my lambda function with the following:

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
**Step 2**
- I created the upload and the download functions

````
Type: AWS::Lambda::Function
    Properties:
      Runtime: python 3.7
      Role: !GetAtt LambdaRole.Arn
      Handler: indexUpload.handler
      Code:
        Zipfile: |
import logging
import boto3
from botocore.exceptions import ClientError


def upload_file(file_name, bucket, object_name=None):
    """Upload a file to an S3 bucket

    :param file_name: File to upload
    :param bucket: Bucket to upload to
    :param object_name: S3 object name. If not specified then file_name is used
    :return: True if file was uploaded, else False
    """

    # If S3 object_name was not specified, use file_name
    if object_name is None:
        object_name = file_name

    # Upload the file
    s3_client = boto3.client('s3')
    try:
        response = s3_client.upload_file(file_name, bucket, object_name)
    except ClientError as e:
        logging.error(e)
        return False
    return True
    ````
    Now the download
    
    ````
      LambdaDownloadFunction:
    Type: AWS::Lambda::Function
    Properties:
      FunctionName: LambdaDownloadFunction
      Role: !GetAtt LambdaRole.Arn
      Runtime: python3.7
      Handler: indexDownload.handler
      Code:
        ZipFile: |
            import boto3
            
            def download_file(bucket, object_name=None, file_name):
            
                # If S3 object_name was not specified, use file_name
                if object_name is None:
                    object_name = file_name
                    
                # Download the file
                s3 = boto3.client('s3')
                try:
                    response = s3.download_file(bucket, object_name, file_name)
                except ClientError as e:
                    logging.error(e)
                    return False
                return True
 ````
