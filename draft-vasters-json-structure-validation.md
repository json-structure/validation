---

title: "JSON Structure: Validation Extensions"
category: info

docname: draft-vasters-json-structure-validation-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: AREA
workgroup:  WG Working Group
keyword:
 - JSON
 - schema
venue:
  group: WG
  type: Working Group
  mail: WG@example.com
  arch: https://example.com/WG
  github: "json-structure/validation"
  latest: "https://json-structure.github.io/validation/draft-vasters-json-structure-validation.html"

author:
 -
    fullname: Clemens Vasters
    organization: Microsoft Corporation
    email: clemensv@microsoft.com

normative:
  RFC2119:
  RFC4646:
  RFC8174:
  JSTRUCT-CORE:
    title: "JSON Structure Core"
    author:
      - fullname: Clemens Vasters
    target: https://json-structure.github.io/core/draft-vasters-json-structure-core.html
  ECMA_262_2022:
    author:
    - org: Ecma International
    date: '2022'
    seriesinfo:
      ECMA Standards: ECMA-262
    target: https://www.ecma-international.org/publications-and-standards/standards/ecma-262/
    title: "ECMAScript\xAE Language Specification"

informative:


--- abstract

The JSON Structure Validation extension provides schema authors with additional
means to constrain instance data. These keywords are applied in conjunction with
the constructs defined in JSON Structure Core. The keywords defined herein include
numeric, string, array, and object validation keywords as well as conditional
validations.

--- middle

# Introduction {#introduction}

The JSON Structure Validation extension provides schema authors with additional
means to constrain instance data. These keywords are applied in conjunction with
the constructs defined in JSON Structure Core
{{JSTRUCT-CORE}}. The keywords defined herein include
numeric, string, array, and object validation keywords as well as conditional
validations.

For each keyword, this document specifies its applicability, the permitted value
types, and the related standards that must be observed.

# Conventions {#conventions}

{::boilerplate bcp14}

# Validation Keywords {#validation-keywords}

## Numeric Validation Keywords {#numeric-validation-keywords}

This section defines the validation keywords applicable to schemas with numeric
types. The value of each keyword MUST be in the value space of the numeric type
to which the keyword is applied.

For schemas with extended numeric types (such as long integers and decimals)
whose base representation is a string, numeric constraint values (e.g., for
`minimum`, `maximum`) MUST be provided as strings.

### `minimum` {#minimum}

An instance is valid if its numeric value is greater than or equal to the value
specified in `minimum`.

Example for basic type:

~~~json
{ "type": "number", "minimum": 10 }
~~~

Example for extended type:

~~~json
{ "type": "decimal", "minimum": "10.00" }
~~~

### `maximum` {#maximum}

An instance is valid if its numeric value is less than or equal to the value
specified in `maximum`.

Example for basic type:

~~~json
{ "type": "number", "maximum": 100 }
~~~

Example for extended type:

~~~json
{ "type": "decimal", "maximum": "100.00" }
~~~

### `exclusiveMinimum` {#exclusiveMinimum}

An instance is valid if its numeric value is strictly greater than the value
specified in `exclusiveMinimum`.

Example for basic type:

~~~json
{ "type": "number", "exclusiveMinimum": 10 }
~~~

Example for extended type:

~~~json
{ "type": "int64", "exclusiveMinimum": "10" }
~~~

### `exclusiveMaximum` {#exclusiveMaximum}

An instance is valid if its numeric value is strictly less than the value
specified in `exclusiveMaximum`.

Example for basic type:

~~~json
{ "type": "number", "exclusiveMaximum": 100 }
~~~

Example for extended type:

~~~json
{ "type": "decimal", "exclusiveMaximum": "100.00" }
~~~

### `multipleOf` {#multipleOf}

An instance is valid if dividing its numeric value by the value of `multipleOf`
results in an integer value. The value of `multipleOf` MUST be a positive
number.

Example for basic type:

~~~json
{ "type": "number", "multipleOf": 5 }
~~~

Example for extended type:

~~~json
{ "type": "decimal", "multipleOf": "5" }
~~~

## String Validation Keywords {#string-validation-keywords}

This section defines the validation keywords applicable to schemas with the type
`string`. The `maxLength` keyword is not included as it is part of JSON
Structure Core and is not redefined here.

### `minLength` {#minLength}

A string is valid if its length is at least the integer value specified in
`minLength`. The value of `minLength` MUST be a non-negative integer.

Example:

~~~json
{ "type": "string", "minLength": 3 }
~~~

### `pattern` {#pattern}

A string is valid if its entire value conforms to the regular expression
provided in the `pattern` keyword. The value of `pattern` MUST be a string
representing a valid regular expression that conforms to the {{ECMA_262_2022}} standard.

Example:

~~~json
{ "type": "string", "pattern": "^[A-Z][a-z]+$" }
~~~

### `format` {#format}

A string is valid if it conforms to a specific format. The value of `format`
MUST be a string. The `format` keyword adds additional standard validation
constraints not covered by the extended types in the core specification without
the need to define an explicit regular expression `pattern`.

- `ipv4` – Internet Protocol version 4 address
- `ipv6` – Internet Protocol version 6 address
- `email` – Email address
- `idn-email` – Internationalized email address
- `hostname` – Hostname
- `idn-hostname` – Internationalized hostname
- `iri` – Internationalized Resource Identifier
- `iri-reference` – Internationalized Resource Identifier reference
- `uri-template` – URI template
- `relative-json-pointer` – Relative JSON pointer
- `regex` – Regular expression

## Array and Set Validation Keywords {#array-and-set-validation-keywords}

This section defines the validation keywords applicable to schemas with the type
`array` and `set`.

### `minItems` {#minItems}

An array or set is valid if its number of elements is at least the integer value
specified in `minItems`. The value of `minItems` MUST be a non-negative integer.

Example:

~~~json
{ "type": "array", "minItems": 2 }
~~~

### `maxItems` {#maxItems}

An array or set is valid if its number of elements does not exceed the integer
value specified in `maxItems`. The value of `maxItems` MUST be a non-negative
integer.

Example:

~~~json
{ "type": "array", "maxItems": 10 }
~~~

### `uniqueItems` {#uniqueItems}

This keyword is only applicable to schemas with the type `array` as this
constraint is inherent to `set`. An array is valid if, when `uniqueItems` is set
to true, no two elements are equal. The value of `uniqueItems` MUST be a boolean
(either true or false).

Example:

~~~json
{ "type": "array", "uniqueItems": true }
~~~

### `contains` {#contains}

An array or set is valid if at least one element satisfies the schema specified
in `contains`. The value of `contains` MUST be a valid JSON Structure object.

Example:

~~~json
{ "type": "array", "contains": { "type": "string" } }
~~~

The condition schema MAY contain a `const` keyword to specify a fixed value that
the array must contain.

Example:

~~~json
{ "type": "array", "contains": { "type": "string", "const": "foo" } }
~~~

### `maxContains` {#maxContains}

An array or set is valid if at most the number of elements specified in
`maxContains` satisfy the schema specified in `contains`. The value of
`maxContains` MUST be a non-negative integer.

Example:

~~~json
{ "type": "array", "contains": { "type": "string" }, "maxContains": 2 }
~~~

### `minContains` {#minContains}

An array or set is valid if at least the number of elements specified in
`minContains` satisfy the schema specified in `contains`. The value of
`minContains` MUST be a non-negative integer.

Example:

~~~json
{ "type": "array", "contains": { "type": "string" }, "minContains": 2 }
~~~

## Object and Map Validation Keywords {#object-and-map-validation-keywords}

This section defines the validation keywords applicable to schemas with the type
`object` and `map`.

### `minProperties` and `minEntries` {#minProperties-and-minEntries}

An object is valid if it has at least as many properties as defined by the
integer value specified in `minProperties`. The value of `minProperties` MUST be
a non-negative integer. The `minEntries` keyword applies equivalently to `map`
types.

Example:

~~~json
{ "type": "object", "minProperties": 1 }
~~~

This constraint is useful for `object` definitions that use dynamic properties
via `additionalProperties` and `patternProperties`.

### `maxProperties` and `maxEntries` {#maxProperties-and-maxEntries}

An object is valid if it contains no more than the integer value specified in
`maxProperties`. The value of `maxProperties` MUST be a non-negative integer.
The `maxEntries` keyword applies equivalently to `map` types.

Example:

~~~json
{ "type": "object", "maxProperties": 5 }
~~~

### `dependentRequired` {#dependentRequired}

This keyword establishes dependencies between object properties. The value is a
map of arrays of strings. Each entry in the map corresponds to a property name
in the object instance. If the property exists, then the properties listed in
the corresponding array MUST also exist in the instance. This keyword does not
apply to the `map` type.

Example:

~~~json
{
  "type": "object",
  "properties": {
    "name": { "type": "string" },
    "credit_card": { "type": "number" },
    "billing_address": { "type": "string" }
  },
  "dependentRequired": {
    "credit_card": ["billing_address"]
  },
  "required": ["name"]
}
~~~

### `patternProperties` and `patternKeys` {#patternProperties-and-patternKeys}

This keyword applies schemas to properties whose names match specified regular
expressions. For each property in the object instance, if its name matches a
regular expression defined in `patternProperties`, then its value MUST validate
against the corresponding schema. The property names used as keys in
`patternProperties` MUST be strings representing valid regular expressions
conforming to the {{ECMA_262_2022}} standard. The `patternKeys` keyword applies
equivalently to `map` types.

Example:

~~~json
{
  "type": "object",
  "patternProperties": {
    "^[A-Z]": { "type": "string" }
  }
}
~~~

> **Note:** All identifiers are additionally subject to the constraints of the
> identifier syntax in JSON Structure Core
> {{JSTRUCT-CORE}}.

### `propertyNames` and `keyNames` {#propertyNames-and-keyNames}

The `propertyNames` keyword validates the names of all properties in an object
against a `string`-typed schema. An object is valid if every property name in
the object is valid. The schema MUST be of type `string`. The `keyNames` keyword
applies equivalently to `map` types.

Example:

~~~json
{
  "type": "object",
  "propertyNames": { "type": "string", "pattern": "^[a-z][a-zA-Z0-9]*$" }
}
~~~

### `has` {#has}

The `has` keyword validates that an object or map has at least one (property)
value that matches the schema. The schema MUST be of type `object`.

Example:

~~~json
{
  "type": "object",
  "has": { "type": "string" }
}
~~~

## Default Values {#default-values}

### `default` {#default}

The `default` keyword provides a default value for a schema. If an instance
matches the schema but does not contain the property, the default value is used.

Example:

~~~json
{
  "type": "object",
  "properties": {
    "name": {
      "type": "string",
      "default": "John Doe"
    }
  }
}
~~~

## Enabling the Extnensions {#enabling-the-extensions}

Validation extensions can be enabled in a schema or
meta-schema by adding the `JSONSchemaValidation` key to the `$uses`
clause when referencing the extended meta-schema:

~~~ json
{
  "$schema": "https://json-structure.github.io/meta/extended/v0/#",
  "$id": "myschema",
  "$uses": [
    "JSONSchemaValidation",
  ],
  "type": "object",
  "properties": {
    "name": {
      "type": "string",
      "pattern": "^[A-Z][a-z]+$"
    }
  }
}
~~~

The extensions are enabled by default in the validation meta-schema:

~~~ json
{
  "$schema": "https://json-structure.github.io/meta/validation/v0/#",
  "$id": "myschema",
  "type": "object",
  "properties": {
    "name": {
      "type": "string",
      "pattern": "^[A-Z][a-z]+$"
    }
  }
}
~~~

# Implementation Considerations {#implementation-considerations}

Validators shall process each validation keyword independently and combine
results using a logical AND conjunction. Regular expression evaluation for
`pattern`, `patternProperties`, and `propertyNames` MUST conform to the
ECMAScript Language Specification {{ECMA_262_2022}}.

# Security Considerations {#security-considerations}

Complex regular expressions specified in `pattern`, `patternProperties`, and
`propertyNames` may lead to performance issues (e.g., ReDoS). Implementations
should mitigate such risks. Overly complex or deeply nested validation
constructs may impact performance and should be optimized.

# IANA Considerations {#iana-considerations}

This document has no IANA actions.

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
