# [<img title="skipper-s3 - S3 adapter for Skipper" src="http://i.imgur.com/P6gptnI.png" width="200px" alt="skipper emblem - face of a ship's captain"/>](https://github.com/balderdashy/skipper-s3) S3 Blob Adapter

[![NPM version](https://badge.fury.io/js/skipper-s3.png)](http://badge.fury.io/js/skipper-s3) &nbsp; &nbsp;
[![Build Status](https://travis-ci.org/balderdashy/skipper-s3.svg?branch=master)](https://travis-ci.org/balderdashy/skipper-s3)

S3 adapter for receiving [upstreams](https://github.com/balderdashy/skipper#what-are-upstreams). Particularly useful for handling streaming multipart file uploads from the [Skipper](https://github.com/balderdashy/skipper) body parser.


## Installation

```
$ npm install skipper-s3 --save
```

Also make sure you have skipper itself [installed as your body parser](http://sailsjs.com/documentation/concepts/Middleware?q=adding-or-overriding-http-middleware).  This is the default configuration in the [Sails framework](https://sailsjs.com).


## Usage

```javascript
req.file('avatar')
.upload({
  // Required
  adapter: require('skipper-s3'),
  key: 'thekyehthethaeiaghadkthtekey',
  secret: 'AB2g1939eaGAdesoccertournament',
  bucket: 'my_stuff',
  // Optional
  token: 'temporary_sts_creds',
  ACL: 'file_permission'
}, function whenDone(err, uploadedFiles) {
  if (err) {
    return res.serverError(err);
  }
  return res.ok({
    files: uploadedFiles,
    textParams: req.params.all()
  });
});
```


For more detailed usage information and a full list of available options, see the Skipper docs, especially the section on "[Uploading to S3](https://github.com/balderdashy/skipper#uploading-files-to-s3)".


## S3-compatible providers (Wasabi, MinIO, etc.)

Two options are forwarded to the underlying `AWS.S3` client so you can point uploads at any S3-compatible provider or at AWS buckets that need path-style addressing:

| Option             | Type     | When to set it                                                                                                                                                              |
| ------------------ | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `endpoint`         | `string` | The provider's S3 endpoint. Leave unset (or `undefined`) to use AWS.                                                                                                        |
| `s3ForcePathStyle` | `bool`   | `true` to use path-style URLs (`https://endpoint/bucket/key`). Required for most non-AWS providers and for AWS buckets whose names contain dots (SSL cert wildcard mismatch). |

Both keys are passed through to `AWS.S3` v2 — see the [AWS SDK constructor docs](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html#constructor-property) for the underlying semantics.

### Example — Wasabi

```javascript
req.file('avatar')
.upload({
  adapter: require('skipper-s3'),
  key:    'YOUR_WASABI_ACCESS_KEY',
  secret: 'YOUR_WASABI_SECRET_KEY',
  bucket: 'my-wasabi-bucket',
  region: 'us-east-2',
  endpoint:         'https://s3.us-east-2.wasabisys.com',
  s3ForcePathStyle: true
}, function whenDone(err, uploadedFiles) { /* … */ });
```

Wasabi requires the region in the credentials to match the region in the endpoint hostname (`us-east-1` ↔ `s3.wasabisys.com`, `us-east-2` ↔ `s3.us-east-2.wasabisys.com`, etc.). Path-style is recommended because Wasabi's virtual-hosted certs don't cover bucket names that contain dots.

### Example — MinIO (self-hosted)

```javascript
req.file('avatar')
.upload({
  adapter: require('skipper-s3'),
  key:    process.env.MINIO_ACCESS_KEY,
  secret: process.env.MINIO_SECRET_KEY,
  bucket: 'uploads',
  region: 'us-east-1', // any value; MinIO ignores it but the SDK requires one
  endpoint:         'http://minio.internal:9000',
  s3ForcePathStyle: true
}, function whenDone(err, uploadedFiles) { /* … */ });
```

### Example — AWS bucket name with dots

```javascript
req.file('avatar')
.upload({
  adapter: require('skipper-s3'),
  key:    'AKIA…',
  secret: '…',
  bucket: 'media.example.com',
  region: 'us-east-1',
  s3ForcePathStyle: true   // no endpoint — still on AWS, just path-style
}, function whenDone(err, uploadedFiles) { /* … */ });
```

Without `s3ForcePathStyle: true`, the SDK builds `https://media.example.com.s3.amazonaws.com/…` and the wildcard cert (`*.s3.amazonaws.com`) won't validate the extra dots.


## Contribute

See [ROADMAP.md](https://github.com/balderdashy/skipper-s3/blob/master/ROADMAP.md).

Also be sure to check out [ROADMAP.md in the Skipper repo](https://github.com/balderdashy/skipper/blob/master/ROADMAP.md).

To run the tests:

```sh
git clone git@github.com:balderdashy/skipper-s3.git
cd skipper-s3
npm install
KEY=your_aws_access_key SECRET=your_aws_access_secret BUCKET=your_s3_bucket npm test
```

Please don't check in your aws credentials :)


## License

**[MIT](./LICENSE)**
&copy; 2013, 2014-

[Mike McNeil](http://michaelmcneil.com), [Balderdash](http://balderdash.co) & contributors

See `LICENSE.md`.

This module is part of the [Sails framework](http://sailsjs.org), and is free and open-source under the [MIT License](http://sails.mit-license.org/).


![image_squidhome@2x.png](http://i.imgur.com/RIvu9.png)


[![githalytics.com alpha](https://cruel-carlota.pagodabox.com/a22d3919de208c90c898986619efaa85 "githalytics.com")](http://githalytics.com/balderdashy/skipper-s3)
