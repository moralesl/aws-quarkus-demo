# Quarkus example project for AWS Lambda

This example project demonstrates how Quarkus can be used to implement lightweight Java-based applications using AWS Lambda.
In this documentation, we'll cover how to

* Compile the application to a native executable
* Packaging of the application in a Custom Runtime for AWS Lambda
* Set up the infrastructure using [AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html)

The infrastructure is set up using AWS SAM and implemented with YAML.

## Application architecture

The architecture of the application is pretty simple and consists of a few classes that implement a REST-service that stores all information in an Amazon DynamoDB-table. Quarkus offers an [extension](https://quarkus.io/guides/dynamodb) for Amazon DynamoDB that is based  on AWS SDK for Java V2. The Quarkus extension supports two programming models:

* Blocking access
* Asynchronous programming

[DynamoDB Local](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html) is also supported: The downloadable version of DynamoDB lets you write and test applications without  accessing the DynamoDB web service. Instead, the database is self-contained on your computer. When you're ready to  deploy your application in production, you can make a few minor changes to the code so that it uses the DynamoDB web service.

Simply run `docker run -p 8000:8000 amazon/dynamodb-local` to execute DynamoDB locally.

## Compile the application

Compiling the application to an Uber-JAR is very straight forward:

```
$ mvn clean install
```

To compile the application to a native binary (a ZIP file also containing `cacerts` and `libsunec.so for proper SSL-handling) we have to add a few parameters:

```
$ mvn clean install -e -Pnative -Dnative-image.docker-build=true
```

## Setting up the infrastructure and deploying the application using AWS SAM

After we've built and pushed the Docker image containing the native image of the application, we need to package our application and deploy it. The SAM package command creates a zip of your code and dependencies and uploads it to S3. SAM deploy creates a Cloudformation Stack and deploys your resources.

The following commands package and deploy the JVM-based version of the application.

```
$ sam package --template-file sam.jvm.yaml --output-template-file output.yaml --s3-bucket <your_s3_bucket>

$ sam deploy --template-file output.yaml --stack-name APIGatewayQuarkusDemo --capabilities CAPABILITY_IAM
```

If you want to deploy the native version of the application, you have to use a different SAM template.

```
$ sam package --template-file sam.native.yaml --output-template-file output.native.yaml --s3-bucket <your_s3_bucket>

$ sam deploy --template-file output.native.yaml --stack-name APIGatewayQuarkusDemo --capabilities CAPABILITY_IAM
```

## Contributing
Please create a new GitHub issue for any feature requests, bugs, or documentation improvements.

Where possible, please also submit a pull request for the change.