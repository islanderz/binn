Binn
====
[![Build Status](https://travis-ci.org/liteserver/binn.svg?branch=master)](https://travis-ci.org/liteserver/binn)
[![Tests](https://img.shields.io/badge/tests-1815-brightgreen.svg)]()
[![Stable](https://img.shields.io/badge/status-stable-brightgreen.svg)]()

Binn is a binary data serialization format designed to be **compact**, **fast** and **easy to use**.


Performance
-----------

The elements are stored with their sizes to increase the read performance.

The library uses zero-copy when reading strings, blobs and containers.

The strings are null terminated so when read the library returns a pointer to them inside the buffer, avoiding memory allocation and data copying.


Data Types
----------

The Binn format supports all these:

Primitive data types:

* null
* boolean (`true` and `false`)
* integer (up to 64 bits signed or unsigned)
* floating point numbers (IEEE single and double precision)
* string
* blob (binary data)
* user defined

Containers:

* list
* map (numeric key associative array)
* object (text key associative array)

Format
--------
The elements are stored in this way:
<pre>
boolean, null:
[type]

int, float (storage: byte, word, dword or qword):
[type][data]

string, blob:
[type][size][data]

list, object, map:
[type][size][count][data]
</pre>

Example Structure
---------------------
A json data such as {"hello":"world"} is serialized in binn as:

<pre>
  \xE2           // type = object (container)
  \x11           // container total size
  \x01           // key/value pairs count
  \x05hello      // key
  \xA0           // type = string
  \x05world\x00  // value (null terminated)
</pre>

You can check the [complete specification](specs.md)

Usage Example
-------------

### C

Writing

```c
binn *obj;

// create a new object
obj = binn_object();

// add values to it
binn_object_set_int32(obj, "id", 123);
binn_object_set_str(obj, "name", "John");
binn_object_set_double(obj, "total", 2.55);

// send over the network or save to a file...
send(sock, binn_ptr(obj), binn_size(obj));

// release the buffer
binn_free(obj);
```

Reading

```c
int id;
char *name;
double total;

id = binn_object_int32(obj, "id");
name = binn_object_str(obj, "name");
total = binn_object_double(obj, "total");
```

### Javascript

Header

```javascript
var binn = require('binn.js');
```

Writing

```javascript
var obj = {hello: 'world', number: 123};
var data = binn.encode(obj);
```

Reading

```javascript
var obj = binn.decode(data);
```

### More examples

You can find more usage examples [here](usage.md) and in the [examples folder](examples)


### Wrappers

Feel free to make a wrapper for your preferred language. Then inform us so we can list it here.


How to use
----------

 1. Including the binn.c file in your project; or
 2. Linking to the binn library:

#### On Linux:
```
gcc myapp.c -lbinn
```

#### On Windows:

Include the `binn-1.0.lib` in your MSVC project


Compiling the Library
---------------------

#### On Linux:

```
git clone https://github.com/liteserver/binn
cd binn
make
sudo make install
```
It will create the file `libbinn.so.1.0`


#### On Windows:

Use the included Visual Studio project in the src/win32 folder

It will create the file `binn-1.0.dll`


Regression Tests
----------------

On Linux:

```
cd binn
make test
```

On Windows:

Use the included Visual Studio project in the test/win32 folder


Reliability
-----------

The current version (1.0) is stable and production ready

As it is cross-platform, data can be transferred between little-endian and big-endian devices


Licence
-------
Apache 2.0


Contact
-------

Questions, suggestions, support: contact AT litereplica DOT io
