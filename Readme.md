# Dockstore CSP: CloudFormation templates for API Gateway + Lambda to CloudWatch logs

This repo contains CloudFormation templates to set up an API gateway that sends
content security policy reports to a lambda function, which filters the reports
and logs them to CloudWatch for analysis.

## Summary of Templates

These CloudFormation templates create the following components:

* API gateway and related infrastructure
* Lambda function called by API gateway
* CloudWatch logs that lambdas use for logging
* Route 53 record to make API gateway available at dockstore-security.org

## Test Lambda Code Locally

To test locally, use the `python-lambda-local` Python package and the files in the `test/` folder.

### Quick start

Quick start instructions for local testing:

* Create a virtual environment
* Install python-lambda-local into virtual environment
* Run your Python lambda function code through python-lambda-local

To create the virtual environment:

```
pip install virtualenv
virtualenv vp
source vp/bin/activate
```

To install python-lambda-local:

```
pip install python-lambda-local
```

To test your code, you need the following three things:

* Name of the Python file with your lambda function code
* Name of function to use as lambda function entry point (must have `f(event, context)` method signature)
* Name of JSON file containing event data


Here is a very simple example of an event data json file:

```
{
    "answer": 42
}
```

Now call the program like this:

```
python-lambda-local [PYTHON_FILE_NAME] -f [ENTRY_FUNCTION_NAME] [EVENT_JSON_FILE]
```

For example:

```
python-lambda-local lam.py -f main event.json
```

## Links

This was a useful example that served as a basis for this CloudFormation template:

* https://gist.github.com/magnetikonline/c314952045eee8e8375b82bc7ec68e88#file-stack-yaml

## Notes

The lambda function code takes two input arguments, event and context.

Here is a summary of an event object from an API gateway invocation of a lambda function:

```
{
    "resource": "Resource path",
    "path": "Path parameter",
    "httpMethod": "Incoming request's method name"
    "headers": {Incoming request headers}
    "queryStringParameters": {query string parameters }
    "pathParameters":  {path parameters}
    "stageVariables": {Applicable stage variables}
    "requestContext": {Request context, including authorizer-returned key-value pairs}
    "body": "A JSON string of the request payload."
    "isBase64Encoded": "A boolean flag to indicate if the applicable request payload is Base64-encode"
}
```

[ref](http://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-set-up-simple-proxy.html#api-gateway-simple-proxy-for-lambda-input-format)

We use `event.body` to get the JSON payload of the CSP reports that we are sent.

