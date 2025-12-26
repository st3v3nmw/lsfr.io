---
title: HTTP API
weight: 1
---

# HTTP API

In this stage, you'll build an in-memory key-value store and expose it over a REST API.

## Endpoints

You'll implement the following endpoints:

1. PUT /kv/{key}

Add or update a key-value pair in the store.

```markdown
PUT /kv/{key}
Parameters:
  - key (path, required): The key to store (cannot be empty)
Body: Value to store as plain text (cannot be empty)
Response:
  - 200 OK: Key-value pair added or updated successfully
  - 400 Bad Request: Return "key cannot be empty\n" or "value cannot be empty\n"
```

2. GET /kv/{key}

Retrieve the value associated with the given key.

```markdown
GET /kv/{key}
Parameters:
  - key (path, required): The key to retrieve
Response:
  - 200 OK: Return the stored value
  - 404 Not Found: Return "key not found\n"
```

3. DELETE /kv/{key}

Remove a key-value pair from the store.

```markdown
DELETE /kv/{key}
Parameters:
  - key (path, required): The key to delete
Response:
  - 200 OK: Key deleted successfully (or key didn't exist)
```

4. DELETE /clear

Remove all key-value pairs from the store.

```markdown
DELETE /clear
Response:
  - 200 OK: All keys cleared successfully
```

5. Error Handling

Unsupported HTTP methods on any endpoint should return:

- 405 Method Not Allowed: Return "method not allowed\n"

> [!WARNING]
> Your API should handle concurrent requests safely.
> Consider [thread safety](https://en.wikipedia.org/wiki/Thread_safety) when implementing your in-memory store.

## Storage

A simple in-memory [map/dictionary](https://en.wikipedia.org/wiki/Associative_array) is sufficient for storage in this stage.
You'll add persistence in the next stage.

### Data Model

Keys and values are stored as simple strings. This keeps the data model straightforward so you can focus on building intuition in distributed systems, not implementing complex data types.

#### Keys

Keys must be URL-safe strings without spaces or forward slashes. Examples of valid keys:

- `country:capital`
- `user_123`
- `special:key-with_symbols.123`

#### Values

Values are stored as UTF-8 encoded text and can contain:

- Unicode characters like 😊
- Spaces and special symbols
- Long strings (up to reasonable memory limits)

## Testing

Your server must accept `--port` and `--working-dir` flags:

```console
$ ./run.sh --port 8080 --working-dir .lsfr/run-20251226-210357
```

The `--working-dir` is where `lsfr` writes logs. Your server's output will be in `primary.log` inside that directory.

You can test your implementation using the `lsfr` command:

```console
$ lsfr test http-api
Testing http-api: HTTP API with GET/PUT/DELETE Operations

✓ PUT Basic Operations
✓ PUT Edge and Error Cases
✓ GET Basic Operations
✓ GET Edge and Error Cases
✓ DELETE Basic Operations
✓ DELETE Edge and Error Cases
✓ Concurrent Operations
✓ Check Allowed HTTP Methods

PASSED ✓

Run 'lsfr next' to advance to the next stage.
```

### Debugging

When tests fail, `lsfr` will show you exactly what went wrong:

```console
$ lsfr test
Testing http-api: HTTP API with GET/PUT/DELETE Operations

✓ PUT Basic Operations
✓ PUT Edge and Error Cases
✓ GET Basic Operations
✗ GET Edge and Error Cases

GET http://127.0.0.1:42409/kv/nonexistent:key
  Expected response: "key not found\n"
  Actual response: "\n"

  Your server should return 404 Not Found when a key doesn't exist.
  Check your key lookup logic and error handling.

FAILED ✗

Read the guide: lsfr.io/kv-store/http-api
```
