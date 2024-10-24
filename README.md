# Machine Learning API in AWS Sagemaker for Penguin Species Classification

This project demonstrates how to build, train, and deploy a machine learning model on AWS SageMaker to classify penguin species using XGBoost. It includes creating an API for predictions using AWS Lambda and API Gateway, and integrates several AWS services such as S3, Lambda, and SageMaker.

The link to the dataset used can be found here: https://www.kaggle.com/datasets/satyajeetrai/palmer-penguins-dataset-for-eda

## Steps to Build and Deploy the Model

### 1. Create SageMaker Notebook Instance
- Go to **Amazon SageMaker** in the AWS console.
- Navigate to **Notebooks** and create a new notebook instance.
  - **Notebook Instance Name**: `build-deploy-model-sagemaker-tutorial`
  - **Instance Type**: `ml.t2.medium`
  - Create a new IAM role and click on **Create Notebook Instance**.
- Open the notebook and select **Conda Python3** as the environment.

### 2. Data Preparation and S3 Bucket
- After splitting the data into training and testing sets in your notebook, open the **S3 bucket** in the AWS console.
- Create a new bucket:
  - **Bucket Name**: `sagemaker-build-and-deploy-penguin-model` (Prefix must start with "sagemaker-").
- Upload the dataset to the S3 bucket (refer to the Jupyter notebook for data preparation steps).

### 3. Train and Deploy the XGBoost Model
- The dataset includes variables like `bill_length_mm`, `bill_depth_mm`, `flipper_length_mm`, and `body_mass_g` for classifying penguin species.
- Train an XGBoost model on 80% of the shuffled data.
- After training, deploy the model and create an inference endpoint in SageMaker.
- Check the endpoint in the **Endpoints** section in the AWS console.

### 4. Create AWS Lambda Function for Inference
- Go to **AWS Lambda** and click on **Create Function**.
  - **Function Name**: `inference-sagemaker-lambda-function`
  - **Runtime**: Python 3.11
- In the code section of the Lambda function, insert the endpoint name from SageMaker in the `endpoint_name` variable.
- Add sample data for inference and configure the Lambda function:
  - **Timeout**: Change the timeout under **General Configuration** from 3 seconds to 1 minute.
  
#### Update Permissions for Lambda
- Go to **IAM** and create a new execution role with the following policies:
  - **AWSLambdaBasicExecutionRole**
  - **AmazonSagemakerFullAccess**
- Name the role: `BasicLambdaSagemakerFullAccessExecutionRole`.
- Assign this role to the Lambda function in the **Permissions** section.

### 5. Test Lambda Function
- After configuring and deploying the Lambda function, test it with a sample input. The expected output will be the species prediction.

### 6. Create API using API Gateway
- Go to **API Gateway** and create a new REST API:
  - **API Name**: `sagemaker-penguin-model`
- Create a POST method under **Actions** and link it to the Lambda function:
  - Integration type: **Lambda Function**
  - Function name: `inference-sagemaker-lambda-function`
  - Check **Use Lambda Proxy Integration**.
- Deploy the API:
  - **Stage Name**: `dev`

### 7. Test API with Postman
- Use **Postman** to test the deployed API:
  - Create a new collection and add a POST request.
  - Retrieve the API URL from the **Stages** section in API Gateway.
  - Set the body of the request to an example:
    ```json
    {
      "x1": 39.1,
      "x2": 18.7,
      "x3": 181.0,
      "x4": 3750.0
    }
    ```
  - Send the request, and the prediction will return `0`, which indicates the **Adelie** species.

## Technologies Used
- **Amazon SageMaker**: For training and deploying the machine learning model.
- **AWS Lambda**: For running the inference function.
- **API Gateway**: For creating an API to interact with the model.
- **S3**: For storing training and test data.
- **XGBoost**: For the machine learning model.
- **Postman**: For testing the API.
- **Python**: Used in the Jupyter notebook for data exploration, modification, model building, and also in the API for data preparation and inference.
