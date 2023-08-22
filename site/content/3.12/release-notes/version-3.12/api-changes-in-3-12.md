---
title: API Changes in ArangoDB 3.12
menuTitle: API changes in 3.12
weight: 20
description: >-
  ArangoDB v3.12 Release Notes API Changes
---
This document summarizes the HTTP API changes and other API changes in ArangoDB 3.12.
The target audience for this document are developers who maintain drivers and
integrations for ArangoDB 3.12.

## HTTP RESTful API

### Behavior changes

#### Collection API

When creating a collection using the `POST /_api/collection` endpoint, the
server log now displays a deprecation message if illegal combinations and
unknown attributes and values are detected in the request body.

Note that all invalid elements and combinations will be rejected in future
versions.

### Privilege changes



### Endpoint return value changes



### Endpoints added



### Endpoints augmented

#### Optimizer rule descriptions

<small>Introduced in: v3.10.9, v3.11.2</small>

The `GET /_api/query/rules` endpoint now includes a `description` attribute for
every optimizer rule that briefly explains what it does.

### Endpoints moved



### Endpoints deprecated



### Endpoints removed

#### JavaScript-based traversal using `/_api/traversal`

The long-deprecated JavaScript-based traversal functionality has been removed
in v3.12.0, including the REST API endpoint `/_api/traversal`.

The functionality provided by this API was deprecated and unmaintained since
v3.4.0. JavaScript-based traversals have been replaced with AQL traversals in
v2.8.0. Additionally, the JavaScript-based traversal REST API could not handle
larger amounts of data and was thus very limited.

Users of the `/_api/traversal` REST API should use
[AQL traversal queries](../../aql/graphs/traversals.md) instead.

## JavaScript API

### Collection creation

When creating a collection using the `db._create()`, `db._createDocumentCollection()`, or
`db._createEdgeCollection()` method, the server log now displays a deprecation message if illegal
combinations and unknown properties are detected in the `properties` object.

Note that all invalid elements and combinations will be rejected in future
versions.

### `@arangodb/graph/traversal` module

The long-deprecated JavaScript-based traversal functionality has been removed in
v3.12.0, including the bundled `@arangodb/graph/traversal` JavaScript module.

The functionality provided by this traversal module was deprecated and
unmaintained since v3.4.0. JavaScript-based traversals have been replaced with
AQL traversals in v2.8.0. Additionally, the JavaScript-based traversals could
not handle larger amounts of data and were thus very limited.

Users of the JavaScript-based traversal API should use
[AQL traversal queries](../../aql/graphs/traversals.md) instead.