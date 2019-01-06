# Couchbase Node.js Client

The Node.js SDK library allows you to connect to a Couchbase cluster from 
Node.js. It is a native Node.js module and uses the very fast libcouchbase 
library to handle communicating to the cluster over the Couchbase binary
protocol.


## Useful Links

Source - [http://github.com/couchbase/couchnode](http://github.com/couchbase/couchnode)

Bug Tracker - [http://www.couchbase.com/issues/browse/JSCBC](http://www.couchbase.com/issues/browse/JSCBC)

Couchbase Developer Portal - [https://docs.couchbase.com/](https://docs.couchbase.com/nodejs-sdk/2.6/start-using-sdk.html)

Release Notes - [https://docs.couchbase.com/nodejs-sdk/2.6/relnotes-nodejs-sdk.html](https://docs.couchbase.com/nodejs-sdk/2.6/relnotes-nodejs-sdk.html)

## Installing

To install the lastest release using npm, run:
```bash
npm install couchbase
```

To install the development version directly from github, run:
```bash
npm install "git+https://github.com/couchbase/couchnode.git#master"
```


## Introduction

Connecting to a Couchbase bucket is as simple as creating a new `Cluster` 
instance to represent the `Cluster` you are using, and then executing
`openBucket` against this to open a connection to you specific bucket.  You
are able to execute most operations immediately, and they will be queued
until the connection is successfully established.

Here is a simple example of instantiating a connection, adding a new document
into the bucket and then retrieving its contents:

### Couchbase < 4.5
```javascript
var couchbase = require('couchbase');
var cluster = new couchbase.Cluster('couchbase://127.0.0.1');
var bucket = cluster.openBucket('default', 'bucketpassword');

bucket.upsert('testdoc', {name:'Frank'}, function(err, result) {
  if (err) throw err;

  bucket.get('testdoc', function(err, result) {
    if (err) throw err;

    console.log(result.value);
    // {name: Frank}
  });
});
```

### Couchbase >= 4.5

Hello Couchbase
```js
var couchbase = require('couchbase')
var cluster = new couchbase.Cluster('couchbase://localhost/');
cluster.authenticate('USERNAME', 'PASSWORD');
var bucket = cluster.openBucket('bucketname');
var N1qlQuery = couchbase.N1qlQuery;

bucket.manager().createPrimaryIndex(function() {
  bucket.upsert('user:frank', {
    'email': 'frank@dspeed.eu', 'interests': ['Holy Grail', 'Serverless', 'Couchbase', 'JavaScript']
  },
  function (err, result) {
    bucket.get('user:frank', function (err, result) {
      console.log('Got result: %j', result.value);
      // { 'user:frank': { 'email': 'frank@dspeed.eu', 'interests': ['Holy Grail', 'Serverless', 'Couchbase', 'JavaScript'] }
      bucket.query(
      N1qlQuery.fromString('SELECT * FROM bucketname WHERE $1 in interests LIMIT 1'),
      ['Couchbase'],
      function (err, rows) {
        console.log("Got rows: %j", rows);
        // Got rows: { 'user:frank': { 'email': 'frank@dspeed.eu', 'interests': ['Holy Grail', 'Serverless', 'Couchbase', 'JavaScript'] }
      });
    });
  });
});
```


## Mock Testing

As part of the core library, we additionally include a powerful Mock version
of the library which supports a significant set of the features that are
available when connected to a real Couchbase Server.

As part of this library, we include a mock version of the client that supports
nearly the exact same feature set as the library itself, but which does not
require that a server be configured.  Note that these Mock connections currently
are per-instance, if another connection is instantiated, none of the data will
be shared.

Using the Mock is as simple as this:
```javascript
var couchbase = require('couchbase').Mock;
var db = new couchbase.Cluster();
var bucket = cluster.openBucket();

bucket.upsert('testdoc', {name:'Frank'}, function(err, result) {
  if (err) throw err;

  bucket.get('testdoc', function(err, result) {
    if (err) throw err;

    console.log(result.value);
    // {name: Frank}
  });
```

## Using an external C SDK

The node.js SDK comes with an embedded version of the C SDK in order to streamline the installation experience. It may be necessary to use an external version of the C SDK if you wish to:

- Use SSL connections with Couchbase
- Use a version of the C SDK contains bug fixes not available in the embedded version
- Develop the SDK itself and wish to speed up build times

To install the Node.js SDK using an external C SDK installation, install the libcouchbase library first, and point npm to it during the install.

```bash
# Assuming you have libcouchbase installed in /usr/local
# such that /usr/local/lib/libcouchbase.so (or .dylib) exists:
npm install couchbase --compile --couchbase-root=/usr/local
```

[Additional Infos about the C SDK](https://docs.couchbase.com/c-sdk/2.10/start-using-sdk.html)

## Documentation

An extensive documentation is available on the Couchbase website.  Visit our
[Node.js Community](http://couchbase.com/communities/nodejs) on
the [Couchbase](http://couchbase.com) website for the documentation as well as
numerous examples and samples.


## Source Control

The source code is available at
[https://github.com/couchbase/couchnode](https://github.com/couchbase/couchnode).
Once you have cloned the repository, you may contribute changes through our
gerrit server.  For more details see
[CONTRIBUTING.md](https://github.com/couchbase/couchnode/blob/master/CONTRIBUTING.md).

To execute our test suite, run `make test` from the root directory.

To execute our code coverage, run `make cover` from the root directory.

In addition to the full test suite and full code coverage, you may additionally
execute a subset of the tests which excludes slow-running tests for quick
verifications.  These can be run through `make fasttest` and `make fastcover`
respectively.

## License
Copyright 2019 Couchbase Inc.

Licensed under the Apache License, Version 2.0.

See
[LICENSE](https://github.com/couchbase/couchnode/blob/master/LICENSE)
for further details.
