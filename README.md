Protocol Buffers - Google's data interchange format
===================================================

[![Build Status](https://travis-ci.org/google/protobuf.svg?branch=master)](https://travis-ci.org/google/protobuf)

Copyright 2008 Google Inc.

This directory contains the JavaScript Protocol Buffers runtime library.

The library is currently compatible with:

1. CommonJS-style imports (eg. `var protos = require('my-protos');`)
2. Closure-style imports (eg. `goog.require('my.package.MyProto');`)

Support for ES6-style imports is not implemented yet.  Browsers can
be supported by using Browserify, webpack, Closure Compiler, etc. to
resolve imports at compile time.

To use Protocol Buffers with JavaScript, you need two main components:

1. The protobuf runtime library.  You can install this with
   `npm install google-protobuf`, or use the files in this directory.
2. The Protocol Compiler `protoc`.  This translates `.proto` files
   into `.js` files.  The compiler is not currently available via
   npm, but you can download a pre-built binary
   [on GitHub](https://github.com/google/protobuf/releases)
   (look for the `protoc-*.zip` files under **Downloads**).


Setup
=====

First, obtain the Protocol Compiler.  The easiest way is to download
a pre-built binary from [https://github.com/google/protobuf/releases](https://github.com/google/protobuf/releases).

If you want, you can compile `protoc` from source instead.  To do this
follow the instructions in [the top-level
README](https://github.com/google/protobuf/blob/master/src/README.md).

Once you have `protoc` compiled, you can run the tests by typing:

    $ cd js
    $ npm install
    $ npm test

This will run two separate copies of the tests: one that uses
Closure Compiler style imports and one that uses CommonJS imports.
You can see all the CommonJS files in `commonjs_out/`.
If all of these tests pass, you know you have a working setup.


Using Protocol Buffers in your own project
==========================================

To use Protocol Buffers in your own project, you need to integrate
the Protocol Compiler into your build system.  The details are a
little different depending on whether you are using Closure imports
or CommonJS imports:

Closure Imports
---------------

If you want to use Closure imports, your build should run a command
like this:

    $ protoc --js_out=library=myproto_libs,binary:. messages.proto base.proto

For Closure imports, `protoc` will generate a single output file
(`myproto_libs.js` in this example).  The generated file will `goog.provide()`
all of the types defined in your .proto files.  For example, for the unit
tests the generated files contain many `goog.provide` statements like:

    goog.provide('proto.google.protobuf.DescriptorProto');
    goog.provide('proto.google.protobuf.DescriptorProto.ExtensionRange');
    goog.provide('proto.google.protobuf.DescriptorProto.ReservedRange');
    goog.provide('proto.google.protobuf.EnumDescriptorProto');
    goog.provide('proto.google.protobuf.EnumOptions');

The generated code will also `goog.require()` many types in the core library,
and they will require many types in the Google Closure library.  So make sure
that your `goog.provide()` / `goog.require()` setup can find all of your
generated code, the core library `.js` files in this directory, and the
Google Closure library itself.

Once you've done this, you should be able to import your types with
statements like:

    goog.require('proto.my.package.MyMessage');

    var message = proto.my.package.MyMessage();

CommonJS imports
----------------

If you want to use CommonJS imports, your build should run a command
like this:

    $ protoc --js_out=import_style=commonjs,binary:. messages.proto base.proto

For CommonJS imports, `protoc` will spit out one file per input file
(so `messages_pb.js` and `base_pb.js` in this example).  The generated
code will depend on the core runtime, which should be in a file called
`google-protobuf.js`.  If you are installing from `npm`, this file should
already be built and available.  If you are running from GitHub, you need
to build it first by running:

    $ gulp dist

Once you've done this, you should be able to import your types with
statements like:

    var messages = require('./messages_pb');

    var message = new messages.MyMessage();

API
===

The API is not well-documented yet.  Here is a quick example to give you an
idea of how the library generally works:

    var message = new MyMessage();

    message.setName("John Doe");
    message.setAge(25);
    message.setPhoneNumbers(["800-555-1212", "800-555-0000"]);

    // Serializes to a UInt8Array.
    bytes = message.serializeBinary();

    var message2 = new MyMessage();
    message2.deserializeBinary(bytes);

For more examples, see the tests.  You can also look at the generated code
to see what methods are defined for your generated messages.
