# Code Generation

To make gRPC requests the client requires generated code for the [method definitions](concepts.md#method-definition) and message classes that are [defined in `.proto` files](https://developers.google.com/protocol-buffers/docs/proto3#services).

[`protoc`](https://github.com/google/protobuf) is the google protobuf code generation tool. It can generate the JavaScript message classes and also supports using plugins for additional code generation.

This process is slightly different between TypeScript and JavaScript usage.

### TypeScript ([skip to JS](#javascript))

[`ts-protoc-gen`](https://www.github.com/improbable-eng/ts-protoc-gen) is a package that can generate the `.d.ts` files that declare the contents of the protoc-generated JavaScript files. `ts-protoc-gen` can also generate `grpc-web-client` service/method definitions with the `protoc-gen-ts` plugin and `service=true` argument.

This is an example of a complete invokation of `protoc` with `ts-protoc-gen` assuming your `.proto` files are in a directory named `my-protos` within the current working directory:

```bash
protoc \
--plugin=protoc-gen-ts=./node_modules/.bin/protoc-gen-ts \
--js_out=import_style=commonjs,binary:my-generated-code \
--ts_out=service=true:my-generated-code \
-I ./my-protos \
my-protos/*.proto
```

A proto file such as `book_service.proto`:

```protobuf
syntax = "proto3";

package examplecom.library;

message Book {
 int64 isbn = 1;
 string title = 2;
 string author = 3;
}

message GetBookRequest {
 int64 isbn = 1;
}

service BookService {
 rpc GetBook(GetBookRequest) returns (Book) {}
}
```

Will generate `book_service_pb.js`, `book_service_pb.d.ts` and `book_service_pb_service.ts`.

The first two files contain the message classes and `book_service_pb_service.ts` contains a `BookService.GetBook` class that acts as method definition that can be used with `grpc-web-client`.


### JavaScript

[`ts-protoc-gen`](https://www.github.com/improbable-eng/ts-protoc-gen) is a package that can generate `grpc-web-client` service/method definitions with the `protoc-gen-js_service` plugin and `js_service_out` argument.

This is an example of a complete invokation of `protoc` with `ts-protoc-gen` assuming your `.proto` files are in a directory named `my-protos` within the current working directory:

```bash
protoc \
--plugin=protoc-gen-js_service=./node_modules/.bin/protoc-gen-js_service \
--js_out=import_style=commonjs,binary:my-generated-code \
--js_service_out=my-generated-code \
-I ./my-protos \
my-protos/*.proto
```

A proto file such as `book_service.proto`:

```protobuf
syntax = "proto3";

package examplecom.library;

message Book {
 int64 isbn = 1;
 string title = 2;
 string author = 3;
}

message GetBookRequest {
 int64 isbn = 1;
}

service BookService {
 rpc GetBook(GetBookRequest) returns (Book) {}
}
```

Will generate `book_service_pb.js` and `book_service_pb_service.js`.

The first file contains the message classes and `book_service_pb_service.js` contains a `BookService.GetBook` class that acts as [method definition](concepts.md#method-definition) that can be used with `grpc-web-client`.
