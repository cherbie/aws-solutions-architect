# Chapter 8 - SQS, SWF and SNS

- **Amazon SQS**
  : fast, reliable, scalable and fully managed message queue service
  - decouple components of a cloud application
  - trasmit any volume of data, any level of throughput, without losing messages or requiring other services to be continously available
  - _SQS_ is effectively a __buffer__ between the application components that _receive_ data and those applications that _process_ the data.
  - ensures delivery of each message at least once
    - should design your system to be _indoponent_
  - service does not garuantee _FIFO_ queueing
    - need to add sequencing information to message packets if it is important
  - messages are _identitified_ by a **globally unique ID** 

!][SQS Message Lifecycle](./images/sqs_lifecycle.png)

- **SQS Message Lifecycle**
  1. Component 1 sends Message A to a queue, and the message is redundantly distributed across the Amazon SQS servers.
  2. When Component 2 is ready to process a message, it retrieves messages from the queue, and Message A is returned. While Message A is being processed, it remains in the queue and is not returned to subsequently receive requests for the duration of the visibility timeout.
  3. Component 2 deletes Message A from the queue to prevent the message from being received and processed again after the visibility timeout expires.

- **Delay Queues**
  : allow you to postpone the delivery of new messages in a queue for a specific number of seconds
  - a message in the queue will be invisible to consumers for the duration of the delay period
  - similar to visibility timeouts

- **Visibility Timeouts**
  : hides a message from consumers after a message is retreived from the queue
  - similar to delay queues

- **In-Flight Message**
  : when a message is neither delayed nor in a visibility timeout
  - up to 120,000 messages in flight at any given time

- **Queue Operations**
  1. `CreateQueue`
  2. `ListQueues`
  3. `DeleteQueue`
  4. `SendMessage`
  5. `SendMessageBatch`
  6. `ReceiveMessage`
  7. `DeleteMessage`
  8. `DeleteMessageBatch`
  9. `PurgeQueue`
  10. `ChanceMessageVisibility`
  11. `ChangeMessageVisibilityBatch`
  12. `SetQueueAttributes`
  13. `GetQueueAttributes`
  14. `GetQueueUrl`
  15. `ListDeadLetterSourceQueues`
  16. `AddPermission`
  17. `RemovePermission`

- **Queue and Message Identifiers**
  : amazon SQS uses **three** identifiers
  1. Queue URLs
  2. Message IDs
  3. Receipt handles

- **Message Attributes**
  : allow you to provide structured metadata items
  - up to 10 attributes per message

- **Long Polling**
  - `ReceiveMessage` function queries the Amazon SQS for messages
  - send a `WaitTimeSeconds` argument to `ReceiveMessage` (up to _20 seconds_)

- **Dead Letter Queue**
  : a queue that other queues target to send messages that for some reason could not be successfully processed
  - primary benefit: ability to _sideline_ and _isolate_ the unsuccessfully processed messages.
  - Analyze any messages sent to the dead leter queue to try to determine the cause of failure

- **Access Control**
  - IAM can be used
  - close coordination between accounts may allow these types of actions through the use of IAM roles, but it is frequently infeasible
  - **Amazon SQS Access Control**
   : allows you to assign policies to queues that grant specific interactions to other accounts without that account having to assume IAM roles from your account
 

