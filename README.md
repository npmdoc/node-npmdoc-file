# api documentation for  [file (v0.2.2)](https://github.com/aconbere/node-file-utils)  [![npm package](https://img.shields.io/npm/v/npmdoc-file.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-file) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-file.svg)](https://travis-ci.org/npmdoc/node-npmdoc-file)
#### Higher level path and file manipulation functions.

[![NPM](https://nodei.co/npm/file.png?downloads=true)](https://www.npmjs.com/package/file)

[![apidoc](https://npmdoc.github.io/node-npmdoc-file/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-file_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-file/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-file/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-file/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Anders Conbere",
        "email": "aconbere@gmail.com"
    },
    "bugs": {
        "url": "http://github.com/aconbere/node-file-utils"
    },
    "dependencies": {},
    "description": "Higher level path and file manipulation functions.",
    "devDependencies": {
        "mocha": "1.9.x"
    },
    "directories": {
        "lib": "lib"
    },
    "dist": {
        "shasum": "c3dfd8f8cf3535ae455c2b423c2e52635d76b4d3",
        "tarball": "https://registry.npmjs.org/file/-/file-0.2.2.tgz"
    },
    "homepage": "https://github.com/aconbere/node-file-utils",
    "license": "MIT",
    "main": "./lib/file",
    "maintainers": [
        {
            "name": "aconbere",
            "email": "aconbere@gmail.com"
        }
    ],
    "name": "file",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+ssh://git@github.com/aconbere/node-file-utils.git"
    },
    "tags": [
        "file",
        "path",
        "fs",
        "walk"
    ],
    "version": "0.2.2"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module file](#apidoc.module.file)
1.  [function <span class="apidocSignatureSpan">file.</span>mkdirs (_path, mode, callback)](#apidoc.element.file.mkdirs)
1.  [function <span class="apidocSignatureSpan">file.</span>mkdirsSync (_path, mode)](#apidoc.element.file.mkdirsSync)
1.  [function <span class="apidocSignatureSpan">file.</span>walk (start, callback)](#apidoc.element.file.walk)
1.  [function <span class="apidocSignatureSpan">file.</span>walkSync (start, callback)](#apidoc.element.file.walkSync)
1.  object <span class="apidocSignatureSpan">file.</span>path

#### [module file.path](#apidoc.module.file.path)
1.  [function <span class="apidocSignatureSpan">file.path.</span>abspath (to)](#apidoc.element.file.path.abspath)
1.  [function <span class="apidocSignatureSpan">file.path.</span>join (head, tail)](#apidoc.element.file.path.join)
1.  [function <span class="apidocSignatureSpan">file.path.</span>relativePath (base, compare)](#apidoc.element.file.path.relativePath)



# <a name="apidoc.module.file"></a>[module file](#apidoc.module.file)

#### <a name="apidoc.element.file.mkdirs"></a>[function <span class="apidocSignatureSpan">file.</span>mkdirs (_path, mode, callback)](#apidoc.element.file.mkdirs)
- description and source-code
```javascript
mkdirs = function (_path, mode, callback) {
  _path = exports.path.abspath(_path);

  var dirs = _path.split(path.sep);
  var walker = [dirs.shift()];

  // walk
  // @ds:  A list of directory names
  // @acc: An accumulator of walked dirs
  // @m:   The mode
  // @cb:  The callback
  var walk = function (ds, acc, m, cb) {
    if (ds.length > 0) {
      var d = ds.shift();

      acc.push(d);
      var dir = acc.join(path.sep);

      // look for dir on the fs, if it doesn't exist then create it, and
      // continue our walk, otherwise if it's a file, we have a name
      // collision, so exit.
      fs.stat(dir, function (err, stat) {
        // if the directory doesn't exist then create it
        if (err) {
          // 2 means it's wasn't there
          if (err.errno == 2 || err.errno == 34) {
            fs.mkdir(dir, m, function (erro) {
              if (erro && erro.errno != 17 && erro.errno != 34) {
                return cb(erro);
              } else {
                return walk(ds, acc, m, cb);
              }
            });
          } else {
            return cb(err);
          }
        } else {
          if (stat.isDirectory()) {
            return walk(ds, acc, m, cb);
          } else {
            return cb(new Error("Failed to mkdir " + dir + ": File exists\n"));
          }
        }
      });
    } else {
      return cb();
    }
  };
  return walk(dirs, walker, mode, callback);
}
```
- example usage
```shell
...


### file.walkSync(start, callback)

Synchronus version of file.walk, calling callback for each directory, passing in (dirPath, dirs, files).


### file.mkdirs(path, mode, callback)

Makes all the directories in a path. (analgous to mkdir -P) For example given a path like "test/this/path" in an empty directory
, mkdirs would make the directories "test", "this" and "path".


### file.mkdirsSync(path, mode)

Like file.mkdirs but synchronous.
...
```

#### <a name="apidoc.element.file.mkdirsSync"></a>[function <span class="apidocSignatureSpan">file.</span>mkdirsSync (_path, mode)](#apidoc.element.file.mkdirsSync)
- description and source-code
```javascript
mkdirsSync = function (_path, mode) {
  if (_path[0] !== path.sep) {
    _path = path.join(process.cwd(), _path)
  }

  var dirs = _path.split(path.sep);
  var walker = [dirs.shift()];

  dirs.reduce(function (acc, d) {
    acc.push(d);
    var dir = acc.join(path.sep);

    try {
      var stat = fs.statSync(dir);
      if (!stat.isDirectory()) {
        throw "Failed to mkdir " + dir + ": File exists";
      }
    } catch (err) {
      fs.mkdirSync(dir, mode);
    }
    return acc;
  }, walker);
}
```
- example usage
```shell
...


### file.mkdirs(path, mode, callback)

Makes all the directories in a path. (analgous to mkdir -P) For example given a path like "test/this/path" in an empty directory
, mkdirs would make the directories "test", "this" and "path".


### file.mkdirsSync(path, mode)

Like file.mkdirs but synchronous.


### file.path.abspath(path)

Expands ".", "..", "~" and non root paths to their full absolute path. Relative paths default to being children of the current working
 directory.
...
```

#### <a name="apidoc.element.file.walk"></a>[function <span class="apidocSignatureSpan">file.</span>walk (start, callback)](#apidoc.element.file.walk)
- description and source-code
```javascript
walk = function (start, callback) {
  fs.lstat(start, function (err, stat) {
    if (err) { return callback(err) }
    if (stat.isDirectory()) {

      fs.readdir(start, function (err, files) {
        var coll = files.reduce(function (acc, i) {
          var abspath = path.join(start, i);

          if (fs.statSync(abspath).isDirectory()) {
            exports.walk(abspath, callback);
            acc.dirs.push(abspath);
          } else {
            acc.names.push(abspath);
          }

          return acc;
        }, {"names": [], "dirs": []});

        return callback(null, start, coll.dirs, coll.names);
      });
    } else {
      return callback(new Error("path: " + start + " is not a directory"));
    }
  });
}
```
- example usage
```shell
...

<pre>
  var file = require("file");
</pre>

## API

### file.walk(start, callback)

Navigates a file tree, calling callback for each directory, passing in (null, dirPath, dirs, files).


### file.walkSync(start, callback)

Synchronus version of file.walk, calling callback for each directory, passing in (dirPath, dirs, files).
...
```

#### <a name="apidoc.element.file.walkSync"></a>[function <span class="apidocSignatureSpan">file.</span>walkSync (start, callback)](#apidoc.element.file.walkSync)
- description and source-code
```javascript
walkSync = function (start, callback) {
  var stat = fs.statSync(start);

  if (stat.isDirectory()) {
    var filenames = fs.readdirSync(start);

    var coll = filenames.reduce(function (acc, name) {
      var abspath = path.join(start, name);

      if (fs.statSync(abspath).isDirectory()) {
        acc.dirs.push(name);
      } else {
        acc.names.push(name);
      }

      return acc;
    }, {"names": [], "dirs": []});

    callback(start, coll.dirs, coll.names);

    coll.dirs.forEach(function (d) {
      var abspath = path.join(start, d);
      exports.walkSync(abspath, callback);
    });

  } else {
    throw new Error("path: " + start + " is not a directory");
  }
}
```
- example usage
```shell
...
## API

### file.walk(start, callback)

Navigates a file tree, calling callback for each directory, passing in (null, dirPath, dirs, files).


### file.walkSync(start, callback)

Synchronus version of file.walk, calling callback for each directory, passing in (dirPath, dirs, files).


### file.mkdirs(path, mode, callback)

Makes all the directories in a path. (analgous to mkdir -P) For example given a path like "test/this/path" in an empty directory
, mkdirs would make the directories "test", "this" and "path".
...
```



# <a name="apidoc.module.file.path"></a>[module file.path](#apidoc.module.file.path)

#### <a name="apidoc.element.file.path.abspath"></a>[function <span class="apidocSignatureSpan">file.path.</span>abspath (to)](#apidoc.element.file.path.abspath)
- description and source-code
```javascript
abspath = function (to) {
  var from;
  switch (to.charAt(0)) {
    case "~": from = process.env.HOME; to = to.substr(1); break
    case path.sep: from = ""; break
    default : from = process.cwd(); break
  }
  return path.join(from, to);
}
```
- example usage
```shell
...


### file.mkdirsSync(path, mode)

Like file.mkdirs but synchronous.


### file.path.abspath(path)

Expands ".", "..", "~" and non root paths to their full absolute path. Relative paths default to being children of the current working
 directory.


### file.path.relativePath(root, fullPath)

Given a root path, and a fullPath attempts to diff between the two to give us an acurate path relative to root.
...
```

#### <a name="apidoc.element.file.path.join"></a>[function <span class="apidocSignatureSpan">file.path.</span>join (head, tail)](#apidoc.element.file.path.join)
- description and source-code
```javascript
join = function (head, tail) {
  if (head == "") {
    return tail;
  } else {
    return path.join(head, tail);
  }
}
```
- example usage
```shell
...


### file.path.relativePath(root, fullPath)

Given a root path, and a fullPath attempts to diff between the two to give us an acurate path relative to root.


### file.path.join(head, tail)

Just like path.join but haves a little more sanely when give a head equal to "". file.path.join("", "tail") returns "tail", path
.join("", "tail") returns "/tail"
...
```

#### <a name="apidoc.element.file.path.relativePath"></a>[function <span class="apidocSignatureSpan">file.path.</span>relativePath (base, compare)](#apidoc.element.file.path.relativePath)
- description and source-code
```javascript
relativePath = function (base, compare) {
  base = base.split(path.sep);
  compare = compare.split(path.sep);

  if (base[0] == "") {
    base.shift();
  }

  if (compare[0] == "") {
    compare.shift();
  }

  var l = compare.length;

  for (var i = 0; i < l; i++) {
    if (!base[i] || (base[i] != compare[i])) {
      return compare.slice(i).join(path.sep);
    }
  }

  return ""
}
```
- example usage
```shell
...


### file.path.abspath(path)

Expands ".", "..", "~" and non root paths to their full absolute path. Relative paths default to being children of the current working
 directory.


### file.path.relativePath(root, fullPath)

Given a root path, and a fullPath attempts to diff between the two to give us an acurate path relative to root.


### file.path.join(head, tail)

Just like path.join but haves a little more sanely when give a head equal to "". file.path.join("", "tail") returns "tail", path
.join("", "tail") returns "/tail"
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
