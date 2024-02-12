# Amazon Q Chatbot User Feedback Analytics Solution


## Overview

This project implements a feedback collection mechanism for Amazon Q chatbot (For Business Use) user feedback, enabling the collection of user ratings and comments on chat responses for analytics and generating recommendations to improve the chatbot's performance.

This solution will send an automated email to a provided email address when an end user of the Amazon Q chatbot provides feedback. The email will contain the feedback comments, the feedback provider, the end user query, the chatbot answer, the resources referenced, and AI recommendations generated by Amazon Bedrock to improve the content in the chatbot's data source and enhance its overall performance.

The feedback data is also saved in JSON format in Amazon S3 so that it can be queried using AWS Glue with Amazon Athena and visualized using Amazon QuickSight.

## Solution Architecture

![Alt text](architecture_diagram.png?raw=true "Architecture")



## Example Feedback Report:

#### Example email report for a serverlessland.com crawled chatbot.  

```
Here is the content report for the given user feedback:

User Details:
- userId: <userid>
- submittedAt: Feb 2, 2024, 2:54:33 PM
- usefulness: NOT_USEFUL
- usefulness_comment: I want to see more examples.

Query:
Why do you use EDA?

Message:
You use EDA for several reasons. EDA provides agility by allowing producers to freely raise events and consumers to come and go to listen to events. As the catalog of events grows over time, more innovation and value can be captured by adding more consumers. EDA also gives organizations the ability to quickly respond to changing business needs. Additionally, EDA provides flexibility such that consumers can come and go without impacting producers. When new business requirements occur, existing events can be listening too and consumers can be created. However, EDA also has some challenges such as error handling, debugging and ensuring idempotency.

Source Attribution Titles:
1. Serverless Land - Good and hard parts of EDA
2. Serverless Land - Event-driven architecture and Conway's law

Source Attribution Snippets:
1. GOOD AND HARD PARTS OF EDA by David Boyne GOOD PARTS Flexibility - The ability for consumers to come and go without impacting the producer. When new business requirements occur, existing events can be listening too and consumers can be created. Process in real time - Some systems can allow for events to be processed in real-time or near-real time. Thinking about the user experience, could this lead to a better UX? Fault tolerance - Consumers are decoupled by nature, if a consumer was to break or fail to process the event, the failure is isolated to that consumer. HARD PARTS Things to think about. Error Handling - When errors occur, being able to track the error and understand what is going on. Need to consider DLQ or ways to capture events before they are gone forever... if that's important to you. Debugging - Producers and consumers are distributed by design, so being able to trace and debug can be hard. Idempotency - Key to think about idempotency. If the same event was to be replayed into your consumer, what will happen?

2. our bounded contexts and domains we start to naturally create domains and systems that exist in their own right and are decoupled from each other (sounds very similar to some of the benefits to event-driven architectures). AGILITY WITH EDA A great benefit of event-driven architectures is the agility it can provide technical teams and also feature development. When producers raise events, consumers come and go and choose to listen to the events (if they are interested). EDA based architectures give organisations the ability to add more consumers to existing events, and as this catalog of events grows over time, more innovation and value can be captured. As you add more consumers, no doubt you might also add new bounded contexts which in reflect would affect your team structure (Conway’s law). CONNECTION BETWEEN EDA, DDD AND CONWAY’S LAW There is a connection between domain-driven design, event-driven architectures and Conway’s law, it’s important to consider that when designing and implementing your applications, you might event see a natural org structure forming around your EDA solution and vice versa. EXTRA RESOURCES

Source Attribution URLs:
1. https://serverlessland.com/event-driven-architecture/visuals/good-and-hard-parts-of-event-architectures
2. https://serverlessland.com/event-driven-architecture/visuals/eda-and-conways-law

Key Suggestion:
Based on the usefulness comment "I want to see more examples", the key suggestion for improving the source content is to provide more concrete examples of how EDA is used in real-world scenarios. This will make the content more useful and appropriate for the user.

Recommendations:
Here are some specific examples that could be added to the content sources:

1. Provide a case study of a company that implemented EDA and the benefits they saw. Show the events they produced, consumers they created, and business impact.

2. Include code snippets demonstrating publishing events, creating event handlers, and processing events asynchronously.

3. Show a step-by-step tutorial for building a simple EDA-based application, explaining each component.

4. Provide examples of common event types like UserSignedUpEvent, OrderShippedEvent, etc. and explain how they flow in an EDA architecture.

5. Interview an engineer at a company using EDA and highlight their experiences


```



# Deploying the Amazon Q (Manual Steps)

[Steps for deploying your Amazon Q web experience](https://docs.aws.amazon.com/amazonq/latest/business-use-dg/deploy-instructions.html)


## Building the Lambda Boto3 layer
This uses Docker to create a boto layer that includes the Business support. The layer artifact is saved in amazon-q-business-user-feedback-solution/cdk/lambdas/layers/ folder.

Build the Layer:

```
$ cd amazon-q-business-user-feedback-solution/cdk/lambdas/layers/
$  ./build_layer.sh
```

This will create the boto_python_layer.zip Lambda layer zip file.


## Deploying the End User Feedback Solution

The project code uses the Python version of the AWS CDK ([Cloud Development Kit](https://aws.amazon.com/cdk/)). To execute the project code, please ensure that you have fulfilled the [AWS CDK Prerequisites for Python](https://docs.aws.amazon.com/cdk/latest/guide/work-with-cdk-python.html).

The project code requires that the AWS account is [bootstrapped](https://docs.aws.amazon.com/de_de/cdk/latest/guide/bootstrapping.html) to allow the deployment of the CDK stack.


### Update the CDK Context Parameters

Update the cdk.context.json with the TO and FROM email addresses

```
    "from_email": "YOUR_FROM_EMAIL",
    "to_email": "YOUR_TO_EMAIL",
    "classification": "confidential"
    "modelid": "anthropic.claude-v2",
    "application_id": "xxxxx-xxxxx-xxxx-xxxx-xxxxx
```
#### Context Parameter Summary

1.	from_email – the from email address that SES will send the feedback report
2.  to_email - to email address that SES will send the feedback report
3.  classification: data classification tag for the s3 bucket default confidential
4.  modelid - bedrock claude model version.  Requires the use of a Claude model modelId.
5.  application_id - Amazon Q for Business applicationid.  For example: xxxxx-xxxxx-xxxx-xxxx-xxxxx


## CDK Deployment

```
# navigate to project directory
cd amazon-q-business-user-feedback-solution/cdk

# install and activate a Python Virtual Environment
python3 -m venv .venv
source .venv/bin/activate

# install dependant libraries
python -m pip install -r requirements.txt

# cdk bootstrap 

$ Upon successful completion of `cdk bootstrap` the project is ready to be deployed.

cdk deploy 
```
Alternative: Run the cdk_deploy.sh script for all the above steps

```
# navigate to project directory
cd amazon-q-business-user-feedback-solution/cdk

# install cdk, setup the virtual environment, bootstrap, and deploy

./cdk_deploy.sh
```

## Cleanup

When you are finished experimenting with this solution, clean up your resources by running the command:

```
cdk destroy 
```


## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

## References

1. [What is Amazon Q (For Business Use)?](https://docs.aws.amazon.com/amazonq/latest/business-use-dg/what-is.html)
2. [Managing Amazon Q applications](https://docs.aws.amazon.com/amazonq/latest/business-use-dg/supported-app-actions.html)
3. [Logging Amazon Q API calls using AWS CloudTrail](https://docs.aws.amazon.com/amazonq/latest/business-use-dg/logging-using-cloudtrail.html)