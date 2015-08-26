go-python
=========

[![Build Status](https://drone.io/github.com/sbinet/go-python/status.png)](https://drone.io/github.com/sbinet/go-python/latest)

Naive `go` bindings towards the C-API of CPython-2.

this package provides a ``go`` package named "python" under which most of the ``PyXYZ`` functions and macros of the public C-API of CPython have been exposed.

theoretically, you should be able to just look at:

  http://docs.python.org/c-api/index.html

and know what to type in your ``go`` program.


this package also provides an executable "go-python" which just loads "python" and then call ``python.Py_Main(os.Args)``.
the rational being that under such an executable, ``go`` based extensions for C-Python would be easier to implement (as this usually means calling into ``go`` from ``C`` through some rather convoluted functions hops)


## Install

With `Go 1` and the ``go`` tool, ``cgo`` packages can't pass anymore
additional ``CGO_CFLAGS`` from external programs (except `pkg-config`)
to the "fake" ``#cgo`` preprocessor directive.

``go-python`` now uses ``pkg-config`` to get the correct location of
headers and libraries.
Unfortunately, the naming convention for the ``pkg-config`` package is
not standardised across distributions and OSes, so you may have to
edit the ``cgoflags.go`` file accordingly.

```sh
 $ go get github.com/sbinet/go-python
```

If ``go get`` + ``pkg-config`` failed:

```sh
 $ cd go-python
 $ edit cgoflags.go
 $ make VERBOSE=1
```

*Note*: you'll need the proper header and `python` development environment. On Debian, you'll need to install the ``python-all-dev`` package

#### OSX

If you are using `homebrew`, install `pkg-config` and write `python-2.7.pc` file manually. It needs.

```
$ brew -v install pkg-config
```

and then, write new file, `/usr/local/lib/pkgconfig/python-2.7.pc`

```
prefix=/System/Library/Frameworks/Python.framework/Versions/2.7
exec_prefix=${prefix}
libdir=${exec_prefix}/lib
includedir=${prefix}/include

Name: Python
Description: Python library
Requires:
Version: 2.7
Libs.private: -lpthread -ldl  -lutil
Libs: -L${libdir} -ldl -lpython2.7
Cflags: -I${includedir}/python2.7 -I${includedir}/python2.7 -fno-strict-aliasing -fno-common -dynamic -g -Os -pipe -fno-common -fno-strict-aliasing -fwrapv -DENABLE_DTRACE -DMACOSX -DNDEBUG -Wall -Wstrict-prototypes -Wshorten-64-to-32 -DNDEBUG -g -fwrapv -Os -Wall -Wstrict-prototypes -DENABLE_DTRACE
```

Documentation
-------------

Is available on ``godoc``:

 http://godoc.org/github.com/sbinet/go-python


Example:
--------

```go
package main

import "fmt"
import "github.com/sbinet/go-python"

func init() {
   err := python.Initialize()
   if err != nil {
          panic(err.Error())
   } 
}

func main() {
 	 gostr := "foo" 
	 pystr := python.PyString_FromString(gostr)
	 str := python.PyString_AsString(pystr)
	 fmt.Println("hello [", str, "]")
}
```

```sh
$ go run ./main.go
hello [ foo ]
```

TODO:
-----

 - fix handling of integers (I did a poor job at making sure everything was ok)

 - add CPython unit-tests

 - do not expose ``C.FILE`` pointer and replace it with ``os.File`` in "go-python" API

 - provide an easy way to extend go-python with ``go`` based extensions

 - think about the need (or not) to translate CPython exceptions into go panic/recover mechanism

 - use SWIG to automatically wrap the whole CPython api ?
