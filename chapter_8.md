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




