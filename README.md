# qRPC

## Abstract

This is an experimental library that try to adopt ideas from gRPC request 
handling and extend it to the Message Broker world.

> **!!! Warning !!!** <br/>
> Do not use it in production.

Is an adoption of a gRPC approach to bind methods and handle requests incoming 
via Message Queue rather than http/2 that originated in gRPC.

## Why we might want that?

If you are building Event Based system at-first, but at the same time some 
services require to process requests, e.g. in case of sequential checks or to 
retrieve historical data fragment, you find yourself in a situation, when RPC 
aproach bacame required.

You might find a solution to that situation, by including additional request 
server, alike REST/JSON, XML-RPC/SOAP, or gRPC/ProtoBuf. 
But this is additional entities to acknowledge, and to become updated in a 
future, they might not be compatible with the rest of your contracts.

Or you might try to handle incoming requests from Message Queue.

## Is it achivable?

Maybe. Soon we will find out.

There are several artefacts that determine what RPC is, a few of them:

- Attribute that helps to identify **Procedure** that should handle particular 
request;
- Attribute that helps to determine one request from another;
- Ability to reply on determined request;
- Attribute/-s that represents incoming arguments.

### Which Message Brokers have those attributes?

| Broker | Out of the box? |
| --- | --- |
| RabbitMQ | Yes |
| Kafka | ? |
| NATS | ? |
| ZeroMQ | ? |

#### RabbitMQ

[RabbitMQ documentation](https://www.rabbitmq.com/direct-reply-to.html) describes 
Reply-to approach to fulfill the RPC scenario. 
Basically, it can be achieved by filling reply-to message header.

## Implementation

There are quite a few stages to accomplish.
Beacuse of we use gRPC as a reference point, the esiest part is to implement 
simple echo client-server, and then change adopt it for message broker, one at first, 
and next the rest.

### Stages

1. Implement gRPC based Echo Client-Server;
2. Identify abstraction complexity to implement Client and Server.