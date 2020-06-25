# GRPC format
References:
* https://developers.google.com/protocol-buffers/docs/proto3

## Packages
This package is overridden (and hence ignored) in some languages.
```
package protocol;
option java_package = "com.couchbase.grpc.protocol";
option go_package = "github.com/couchbaselabs/transactions-fit-performer/protocol";
```

Our goal is to have `<package-name>.<message-or-enum-name>`, in every language.  E.g. the .proto filename isn't used.
This will keep refactoring simple as it will allow messages and enums to be freely moved between .proto files.

## Java Specifics
This will produce one Java class for each enum & message, in the com.couchbase.grpc.protocol package.  Default
behaviour is to wrap those classes in a class named after this .proto file, which makes refactoring hard.
```
option java_multiple_files = true;
```
