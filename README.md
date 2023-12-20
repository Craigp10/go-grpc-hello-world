# go gRPC Practice Quick start application to review the basics

## Hello World

# Client-Server architecture utilizing protobufs to define the services and structures

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
