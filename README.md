# jsonschema

[![License](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](https://opensource.org/licenses/BSD-3-Clause)
[![GoDoc](https://godoc.org/github.com/santhosh-tekuri/jsonschema?status.svg)](https://godoc.org/github.com/santhosh-tekuri/jsonschema)
[![Go Report Card](https://goreportcard.com/badge/github.com/santhosh-tekuri/jsonschema)](https://goreportcard.com/report/github.com/santhosh-tekuri/jsonschema)
[![Build Status](https://travis-ci.org/santhosh-tekuri/jsonschema.svg?branch=master)](https://travis-ci.org/santhosh-tekuri/jsonschema)
[![codecov.io](https://codecov.io/github/santhosh-tekuri/jsonschema/coverage.svg?branch=master)](https://codecov.io/github/santhosh-tekuri/jsonschema?branch=master)

Package jsonschema provides draft4 json-schema compilation and validation.

An implementation of JSON Schema, based on IETF's draft v4. 

Passes all tests(including optional) in https://github.com/json-schema/JSON-Schema-Test-Suite

An example of using this package:

```go
schemea, err := jsonschema.Compile("schemas/purchaseOrder.json")
if err != nil {
    return err
}
if err = schema.Validate("purchaseOrder.json"); err != nil {
    return err
}
```

This package supports loading json-schema from filePath and fileURL.

To load json-schema from HTTPURL, add following import:

```go
import _ "github.com/santhosh-tekuri/jsonschema/httploader"
```

Loading from urls for other schemes (such as ftp), can be plugged in. see package jsonschema/httploader
for an example

To load json-schema from in-memory:

```go
data := []byte(`{"type": "string"}`)
url := "sch.json"
compiler := jsonschema.NewCompiler()
if err := compiler.AddResource(url, data); err != nil {
    return err
}
schemea, err := jsonschema.Compile(url)
if err != nil {
    return err
}
if err = schema.Validate("doc.json"); err != nil {
    return err
}
```

This package supports json string formats: 
- date-time
- hostname
- email
- ip-address
- ipv4
- ipv6
- uri
- uriref/uri-reference
- regex
- format

Developers can define their own formats using package jsonschema/formats.

## ValidationError

The ValidationError returned by Validate method contains detailed context to understand why and where the error is.

schema.json:
```json
{
      "$ref": "t.json#/definitions/employee"
}
```

t.json:
```json
{
    "definitions": {
        "employee": {
            "type": "string"
        }
    }
}
```

doc.json:
```json
1
```

Validating `doc.json` with `schema.json`, gives following ValidationError:
```
I[#] S[#] doesn't validate with "schema.json#"
  I[#] S[#/$ref] doesn't valide with "t.json#/definitions/employee"
    I[#] S[#/definitions/employee/type] expected string, but got number
```

Here `I` stands for instance document and `S` stands for schema document.  
The json-fragments that caused error in instance and schema documents are represented using json-pointer notation.  
Nested causes are printed with indent.

## CLI

```bash
jv <schema-file> [<json-doc>]...
```

if no `<json-doc>` arguments are passed, it simply validates the `<schema-file>`.

exit-code is 1, if there are any validation errors
