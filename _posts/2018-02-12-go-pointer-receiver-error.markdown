---
layout: post
title:  "golang pointer receiver error"
date:   2018-02-12 00:00:00 +0000
categories: go golang error
---

Assuming we have an interface declaration in go as so:

```
type Stringer interface {
  String() string
}
```
We can create a custom struct to implement the interface like so:
```
type struct MyStruct{
  Value string
}

func (m *MyStruct) String() string {
  return m.Value
}
```
If we try to assign a type of MyStruct to the Stringer interface, we will receive an error of __ MyType does not implement Stringer (String method has pointer receiver)__

```
mytype := MyStruct{Value: "test"}

var s Stringer
s = m // throws the error above
```

This is because the interface is defined on pointer types of __*MyType__ and not the types of __MyType__

To fix the error we just need to use the pointer type:

```
mytype := MyStruct{Value: "test"}

var s Stringer
s = &m // no errors
```
