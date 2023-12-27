# go gRPC Practice Quick start application to review and annotate the basics.

# Hello World

## Client-Server architecture utilizing protobufs to define the services and structures

## helloworld/

Service definition using Protobufs.

1. Define proto version (syntax) and options for service.
2. Define service `helloworld` and function `sayHello`.
3. Define request `HelloRequest` and response `HelloReply` for `sayHello` service structures.
4. Repeat 2 for additional service endpoint `SayHelloAgain` using step 3's structures,
   `rpc SayHelloAgain (HelloRequest) returns (HelloReply) {}`.
5. Compile the service into Go using `protoc` and the command below from the project root directory.

   ```$ protoc --go_out=. --go_opt=paths=source_relative \
   --go-grpc_out=. --go-grpc_opt=paths=source_relative \
   helloworld/helloworld.proto
   ```

   Additional files should be created that contain the go definitions and func signatures necessary for the server.
   The above command will need to be ran whenever a new service or definition is made to the proto. Update the proto before
   calling the service on the server. Compile before calling the rpc function from the client.

## server/

Implements go-grpc w/ a Greeter service over port 50051, supporting 2 functions. Listing over a tcp connection.
Using the pb Greeter service to implement the grpc interface.

## client/

Creates a connection to the grpc greeter service and calls the 2 endpoints with a default or provided name.

## Further exploration

### The gRPC protocol allows you to define 4 different kinds of service methods

But first let's define the formats

<b> Standard input output </b> is a standard object that we defined in the protobuf. 1 message structure
<b> Stream input output </b> is a stream of objects defined in the protobuf, multuple messaged structure

1. Standard:Standard - urnary rpc, standard client server request/response
2. Standard:Stream - server streaming, client recieves stream and reads from it until closed by server
3. Stream:Standard - client streaming, client streams messages to server once finished will wait for response from server
4. Stream:Stream - Bidirectional streaming, server and client can write/read in any order, the order is kept for the stream.

### Using the API

<b>Server Side</b> On the server, the rpc decodes incoming messages and invokes functions defined in the proto service

<b>Client Side</b> On the client, a local 'stub' is created of the server rpc. This stub has access to methods defined in the service.

### Lifecycles

1. Urnary -
   `rpc SayHello(HelloRequest) returns (HelloResponse);`
   a. Client calls stub function, server is notified with client metadata and timeout deadline.
   b. Server can response based on metadata, it's own metadata or wait to recieve the request (which happens first depends on application setup).
   c. Server recieves request, then work begins. Server creates response.
   d. Client receives response and call on client side is complete, it ends.
2. Server Streaming -
   `rpc LotsOfReplies(HelloRequest) returns (stream HelloResponse);`
   a. Same as Urnary, except server sends stream of messages. At the end of the stream the status and additional information may be appended and connection is closed.
3. Client Streaming -
   `rpc LotsOfGreetings(stream HelloRequest) returns (HelloResponse);`
   a. Similar as above except server will almost always wait to send metadata back until it receives all messages from client.
4. Bidirectional Streaming -
   `rpc BidiHello(stream HelloRequest) returns (stream HelloResponse);`
   a. Client invokes connection/stream, server can choose to send metadata back or not until it starts to receive messages from client
   b. Messaging begins, orders are kept.
   c. <b> How does it end?</b>

### Deadline/Timeout

The client will specify a timeout period its willing to wait for invoke. On the server side, you can query to see which/ how many invokes have timedout.

### Termination

In gRPC, both the client and server make independent and local determinations of the success of the call, and their conclusions may not match. This means that, for example, you could have an RPC that finishes successfully on the server side (“I have sent all my responses!”) but fails on the client side (“The responses arrived after my deadline!”). It’s also possible for a server to decide to complete before a client has sent all its requests.

### Metadata

Partictular information about the rpc in key-value pairs, could be binary. User defined metadata is not used by gRPC, so this allows specific configuration to be added for the client/server to use.

### Channel

Can be used to create a client stub, using a port and host. A channel has state, including connected and idle. Some languages allow querying channel state.

# More Information Examples

[here](https://grpc.io/docs/guides/)
