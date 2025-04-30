pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-access-key-id')
        AWS_DEFAULT_REGION = 'us-east-1'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Lambda') {
            steps {
                sh 'npm install'
            }
        }

        stage('Deploy Lambda') {
            steps {
                sh '''
                    zip function.zip index.js
                    aws lambda update-function-code --function-name MyLambdaFunction --zip-file fileb://function.zip
                '''
            }
        }

        stage('Deploy API Gateway') {
            steps {
                sh '''
                    REGION=us-east-1
                    FUNCTION_NAME=MyLambdaFunction
                    API_NAME=MyLambdaAPI

                    # Create API
                    API_ID=$(aws apigateway create-rest-api --name $API_NAME --region $REGION --query 'id' --output text)

                    # Get Root Resource ID
                    ROOT_ID=$(aws apigateway get-resources --rest-api-id $API_ID --region $REGION --query 'items[0].id' --output text)

                    # Create Resource and Method
                    RESOURCE_ID=$(aws apigateway create-resource --rest-api-id $API_ID --parent-id $ROOT_ID --path-part lambda --region $REGION --query 'id' --output text)

                    aws apigateway put-method --rest-api-id $API_ID --resource-id $RESOURCE_ID --http-method GET --authorization-type NONE --region $REGION

                    # Integrate with Lambda
                    LAMBDA_ARN=$(aws lambda get-function --function-name $FUNCTION_NAME --region $REGION --query 'Configuration.FunctionArn' --output text)

                    aws apigateway put-integration \
                        --rest-api-id $API_ID \
                        --resource-id $RESOURCE_ID \
                        --http-method GET \
                        --type AWS_PROXY \
                        --integration-http-method POST \
                        --uri arn:aws:apigateway:$REGION:lambda:path/2015-03-31/functions/$LAMBDA_ARN/invocations \
                        --region $REGION

                    # Allow API Gateway to access Lambda
                    aws lambda add-permission \
                        --function-name $FUNCTION_NAME \
                        --statement-id allow-apigateway \
                        --action lambda:InvokeFunction \
                        --principal apigateway.amazonaws.com \
                        --source-arn arn:aws:execute-api:$REGION:*:$API_ID/*/GET/lambda \
                        --region $REGION || true

                    # Deploy to prod
                    aws apigateway create-deployment --rest-api-id $API_ID --stage-name prod --region $REGION

                    echo "✅ API URL: https://$API_ID.execute-api.$REGION.amazonaws.com/prod/lambda"
                '''
            }
        }
    }
}
