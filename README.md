# anyfs

[![npm](https://img.shields.io/npm/v/anyfs.svg?style=flat-square)](https://www.npmjs.com/package/anyfs)
[![npm](https://img.shields.io/npm/dm/anyfs.svg?style=flat-square)](https://www.npmjs.com/package/anyfs)
[![Travis](https://img.shields.io/travis/anyfs/anyfs.svg?style=flat-square)](https://travis-ci.org/anyfs/anyfs)
![npm](https://img.shields.io/npm/l/anyfs.svg?style=flat-square)

AnyFS is a portable filesystem abstraction for Node. It aims to provide a 
consistent API for different file systems. 

WARNING: AnyFS is under heavy development, things may change at any time!
a
## Features

- Extensible with plugins
- Super portable with file system adapters
- Works well with Gulp (vinyl-fs plugin)
- API with Promise support

## Adapters

AnyFS comes with following adapters.

- [Dropbox](https://github.com/anyfs/dropbox-adapter) - NPM: anyfs-dropbox-adapter
- [FTP](https://github.com/anyfs/ftp-adapter) - NPM: anyfs-ftp-adapter
- [AWS S3](https://github.com/anyfs/s3-adapter) - NPM: anyfs-s3-adapter
- Memory - Builtin, access with `AnyFS.MemoryAdapter`
- <del>[Local](https://github.com/anyfs/local-adapter): local file system</del>
- <del>SFTP</del>
- <del>Baidu</del>
- <del>GIT</del>
- <del>SVN</del>

## Plugins

- Core: builtin, basic filesystem support.
- [glob](https://github.com/anyfs/glob-plugin): match files easily.
- [vinyl-fs](https://github.com/anyfs/vinyl-fs-plugin): vinyl-fs port, works well with gulp

## Usage

```js
var AnyFs = require('anyfs');
var FtpAdapter = require('anyfs-ftp-adapter');
var DropboxAdapter = require('anyfs-dropbox-adapter');
var VinylFsPlugin = require('anyfs-vinyl-fs-plugin');
AnyFS.addPlugin(new VinylFsPlugin());

var fs1 = new AnyFS(new FtpAdapter({
    server: 'ftp.example.com',
    username: 'user',
    password: 'password',
}));

var fs2 = new AnyFS(new DropboxAdapter({
    key: 'appkey',
    secret: 'appsecret',
    token: 'token',
}));

// Copy files across filesystems(requires the vinyl-fs plugin)
fs1.src('/**/*.jpg')
    .pipe(fs2.dest('/backup/abc/'));

// Promise style API
fs1.mkdir('/doc')
    .then(function() {
        return this.writeFile('/doc/index.md', "content");
    })
    .then(function() {
        return this.metadata('/doc/index.md');
    })
    .done(function(metadata) {
        console.log(metadata.size);
    }, function(err) {
        console.log('Error occured: ', err);
    });

// callback API
fs.mkdir('/doc', function(err) {
    if (err) {
        console.log(err);
    } else {
        console.log('mkdir ok');
    }
});
```

## API

### Core API

Following APIs are basic file system APIs.

#### `constructor(adapter, options)`

The constructor accepts an adapter and an options object.

Common options: 

- cwd: Current working directory.

#### `metadata(path[, callback(error, metadata)])`

Retrieves file and folder metadata.

Folder metadata:

```js
{
    "name": "dir1",
    "time": [Date Object],
    "is_dir": true,
}
```

File metadata:

```js
{
    "name": "file1.txt",
    "time": [Date Object],
    "is_dir": false,
    "size": 123,
    ...
}
```

If callback is not provided, a promise is returned.

#### `list(path[, callback(error, list)])`

Get contents of directory.

```
[
    {
        // metadata
    },
    ...
]
```

#### `mkdir(path[, callback(error)])`

Create directory recursively.

If callback is not provided, a promise is returned.

#### `delete(path[, callback(error)])`

Delete file.

If callback is not provided, a promise is returned.

#### `deleteDir(path[, callback(error)])`

Delete directory recursively.

If callback is not provided, a promise is returned.

#### `move(oldPath, newPath[, callback(error)])`

Move file or directory to a new place.

Parent folder of `newPath` is created automaticly.

If callback is not provided, a promise is returned.

#### `writeFile(path, content[, options][, callback(error)])`

Write file content, will try to create parent directory.

If callback is not provided, a promise is returned.

#### `readFile(path[, options][, callback(error, data)])`

Read file content.

If callback is not provided, a promise is returned.

#### `createWriteStream(path[, options])`

Create write stream.

#### `createReadStream(path[, options])`

Create read stream.

### Extra API

Extra APIs are supported by plugins

## Create Custom Adapters

See [adapter specification](adapter.md)

## Create Plugins

## Acknowledgement

Inspired by [Flysystem](http://flysystem.thephpleague.com/)
