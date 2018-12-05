# ifacemaker

This is a development helper program that generates a Golang interface by inspecting
the structure methods of an existing `.go` file. The primary use case is to generate
interfaces for gomock, so that gomock can generate mocks from those interfaces. This
makes unit testing easier.

## Install

```
go get github.com/vburenin/ifacemaker
```

## Usage
Here is the help output of ifacemaker:

```
$ ifacemaker --help
Usage:
  ifacemaker [OPTIONS]

Application Options:
  -f, --file=          Go source file to read
  -s, --struct=        Generate an interface for this structure name
  -i, --iface=         Name of the generated interface
  -p, --pkg=           Package name for the generated interface
  -y, --iface-comment= Comment for the interface, default is '// <iface> ...'
  -d, --doc=           Copy docs from methods (default: true)
  -D, --type-doc       Copy type doc from struct
  -c, --comment=       Append comment to top
  -o, --output=        Output file name. If not provided, result will be printed to stdout.

Help Options:
  -h, --help           Show this help message
$
```

As an example, let's say you wanted to generate an interface for the Human structure
in this sample code:

```
package main

import "fmt"

type Human struct {
	name string
	age  int
}

// Returns the name of our Human.
func (h *Human) GetName() string {
	return h.name
}

// Our Human just had a birthday! Increase its age.
func (h *Human) Birthday() {
	h.age += 1
	fmt.Printf("I am now %d years old!\n", h.age)
}

// Make the Human say hello.
func (h *Human) SayHello() {
	fmt.Printf("Hello, my name is %s, and I am %d years old.\n", h.name, h.age)
}

func main() {
	human := &Human{name: "Bob", age: 30}
	human.GetName()
	human.SayHello()
	human.Birthday()
}
```

The ifacemaker helper program can generate this interface for you:

```
$ ifacemaker -f human.go -s Human -i HumanIface -p humantest -y "HumanIface makes human interaction easy" -c "DONT EDIT: Auto generated"
// DONT EDIT: Auto generated

package humantest

// HumanIface makes human interaction easy
type HumanIface interface {
	// Returns the name of our Human.
	GetName() string
	// Our Human just had a birthday! Increase its age.
	Birthday()
	// Make the Human say hello.
	SayHello()
}

$
```

In the above example, ifacemaker inspected the structure methods of the Human struct
and generated an interface called HumanIface in the humantest package. Note that the
ifacemaker program preserves docstrings by default.

You can tell ifacemaker to write its output to a file, versus stdout, using the `-o`
parameter:

```
$ ifacemaker -f human.go -s Human -i HumanIface -p humantest -y "HumanIface makes human interaction easy" -c "DONT EDIT: Auto generated" -o humaniface.go
$
```
