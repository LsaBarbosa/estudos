#AWS Lambda

**AWS Lambda** is an AWS **serverless** compute service that allows you to run code without the need to provision or manage servers.

You simply write the function, deploy it to AWS, and define the events that will trigger it.

AWS handles:

*   provisioning servers
*   starting the application
*   automatic scaling
*   applying security patches
*   monitoring the infrastructure

The developer is responsible only for the business logic.

---

# A Lambda function

An AWS Lambda executes a single, small unit of code.

For example:

```
Receive request → Validate data → Write to database → Return response
```
- Each function typically has a specific responsibility.
- A Lambda does not run on its own; it is triggered by an event.
- HTTP request via API Gateway, SNS or SQS message...

- AWS automatically creates new instances of the function.
- You do not need to configure new servers.
- Lambda consumes resources only when it executes.
- It is stateless; it should not rely on memory persistence between executions.
- It functions as a core component in event-driven architectures.

---

# Observability

You can monitor a Lambda using:

*   **CloudWatch Logs** for logs
*   **CloudWatch Metrics** for metrics on duration, errors, and invocation count
*   **AWS X-Ray** for distributed tracing
*   **CloudWatch Alarms** for alerts

---

# Common use cases

*   REST APIs with API Gateway
*   Processing files uploaded to S3
*   Synchronous processing of SQS messages
*   EventBridge event consumers
*   Small-volume ETL
*   Task automation
*   Integrations between AWS services
*   Webhooks
*   Backends for web and mobile applications

---

# Interview summary

> AWS Lambda is an AWS serverless service that executes functions in response to events, without the developer needing to manage servers. The platform automatically handles infrastructure provisioning, scaling, and maintenance. The pricing model is based on invocations and execution time, and the function can be triggered by services such as API Gateway, S3, SQS, EventBridge, and DynamoDB. Key concepts include cold starts, auto-scaling, stateless execution, IAM integration, and observability via CloudWatch and X-Ray. In Java, it is commonly used for APIs, event processing, and integrations within distributed architectures.
