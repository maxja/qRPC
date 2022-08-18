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
simple client-server, and then change adopt it for message broker, one at first, 
and next the rest.

The key point, is to use generated code produced by _protoc-gen-go-grpc_ as a 
starting point. It gives enough of abstraction to begin work with.

Good start will be to use example from gRPC project and build-up new building 
blocks to support same functionality.

### Stages

#### Reference exmaples from gRPC project and remove go mod's replace.

Examples folder contains entrypoint file to run them at once `exmaples_test.sh`. 

First example that runs, is Hello World. 

Server application contains `server` struct declared with embedement of 
pre-generated structure from protofile, method `SayHello` within `server` 
receiver, and next three important lines:
```golang
s := grpc.NewServer()
pb.RegisterGreeterServer(s, &server{})
s.Serve(lis)
```
**TODO:**

1. [ ] Initialization of a server instance `grpc.NewServer` should be mocked 
with analogous `grpc.NewServer`;
2. [ ] Server instance registration with defined `server` struct via protobuf 
provided method. For that we have to mimic original server interface that 
`RegisterGreeterServer` awaits;
3. [ ] Define our own `Serve` method;

Client application establish tcp connection and pass it as an initialization 
argument to the pre-generated from protobuf Client structure, then invoke it's 
method.

```golang
conn, err := grpc.Dial(*addr, grpc.WithTransportCredentials(insecure.NewCredentials()))
c := pb.NewGreeterClient(conn)
r, err := c.SayHello(ctx, &pb.HelloRequest{Name: *name})
```

For our luck, `NewGreetClient` consumes interface, so we can mock it.

**TODO:**

4. [ ] Implement client that adopt required interface.
