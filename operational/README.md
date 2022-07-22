# GRPC format
References:
* https://developers.google.com/protocol-buffers/docs/proto3

## Packages
The following rules are used for packages:

1. A flat directory structure is used so performers can pull in all protobuf files easily.
2. As a replacement for a directory structure, the filename includes the package name.  E.g. `sdk.kv.options.proto` which is in package `protocol.sdk.kv`.
3. The "protocol.shared" package is used for all shared/base code: anything that could be shared between SDK and transactions, for instance.
4. Otherwise aim to put new GRPC into a package, or create a package.  Most will go under `sdk`, e.g. `sdk.query`.
5. Avoid import package cycles (see below) to allow Go to compile.
6. We use 'sdk.kv.Get' naming, rather than say 'sdk.kv.SdkKvGet'.
   1. One exception to this are transactions, e.g. `transactions.TransactionResult`.  These messages existed prior to this rule, and to reduce breakage, they were not renamed.
7. Don't have filenames that match message/enum names.  E.g. "PerformerCaps" enum exists, so can't have "performer_caps.proto".  It produces non-compilable Java.
8. For simplicity we keep a one-to-one mapping between the GRPC package and the generated packages.  E.g.:
   ```
   package protocol.sdk.kv;
   option csharp_namespace = "Couchbase.Grpc.Protocol.Sdk.Kv";
   option java_package = "com.couchbase.client.performer.protocol.sdk.kv";
   ```

## Import package cycles
A Go-specific issue exists that impacted the design:

```
a.proto:
package protocol;
import "c.proto";

b.proto:
package protocol;

c.proto:
package protocol.sdk;
import "b.proto";
```

Go cannot compile this setup as c.proto is importing from the top-level package again, and Go sees this as an import cycle.

## Java Specifics
This will produce one Java class for each enum & message, in the com.couchbase.grpc.protocol package.  Default
behaviour is to wrap those classes in a class named after this .proto file, which makes refactoring hard.
```
option java_multiple_files = true;
```
