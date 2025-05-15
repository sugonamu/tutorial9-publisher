# Publisher — Event-Driven Architecture Tutorial

## ❓ How much data will the publisher send to the message broker in one run?

In one execution, the publisher sends **5 events** to the RabbitMQ message broker.  
Each event is a serialized `UserCreatedEventMessage` which consists of:

```rust
pub struct UserCreatedEventMessage {
    pub user_id: String,
    pub user_name: String,
}
```

The size of each message depends on the length of the strings, but typically each message is around 50–100 bytes when serialized with Borsh.

Thus, the total data sent in one run is approximately 250–500 bytes — very lightweight and efficient for message-based systems.

## What does the URL amqp://guest:guest@localhost:5672 mean?
This URL defines how the application connects to the RabbitMQ server using the AMQP (Advanced Message Queuing Protocol).

amqp://
` Indicates the communication protocol used — AMQP, which is a standard for message brokers like RabbitMQ.

guest:guest
- These are the default username and password used to authenticate with RabbitMQ:

- The first guest is the username

- The second guest is the password

localhost:5672
- This specifies the host and port of the RabbitMQ server:

- localhost means the server is running on the same machine

5672 is the default port used by RabbitMQ for AMQP connections

This URL is the same in both the publisher and subscriber because they are both connecting to the same message broker instance, running locally on the same machine.