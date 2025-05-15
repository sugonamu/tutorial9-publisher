# Publisher — Event-Driven Architecture Tutorial

## How much data will the publisher send to the message broker in one run?

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

## RabbitMQ Dashboard

Screenshot of the RabbitMQ dashboard after setup:

![RabbitMQ Dashboard](./rabbitmq_dashboard.png)

## Sending and Processing Events

When I run `cargo run` on the publisher, it sends **5 `UserCreatedEventMessage` events** to the RabbitMQ message broker. 

Each event includes a `user_id` and a `user_name` tagged with my NPM.

As soon as the events are published:

- RabbitMQ receives and queues them on the `user_created` queue.
- The subscriber (already running) picks up each event and processes them one by one.
- The subscriber prints the content of each message to the terminal.

This setup demonstrates **asynchronous decoupling** between services — the publisher doesn’t need to wait for the subscriber to finish processing.


#### Publisher Output

![Publisher Terminal](publishercmd.png)

#### Subscriber Output

![Subscriber Terminal](subscribercmd.png)

#### RabbitMQ Spike (Dashboard)

![RabbitMQ Queue Spike](chart.png)

---

### Explaining the Spike in RabbitMQ

The spike seen in the **RabbitMQ dashboard** occurs because the publisher rapidly sends multiple events at once, causing a brief increase in queued messages.  
Since the subscriber consumes events more slowly (especially with delays like `thread::sleep`), the dashboard shows:

- A sudden **increase** in the "Queued messages" graph
- A **gradual decrease** as the subscriber processes them

This spike visually confirms that RabbitMQ is correctly buffering the events and delivering them to consumers at their own pace, which is a key benefit of **event-driven architecture**.

---

