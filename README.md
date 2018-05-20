# Kavy
Networked in-memory key-value store, written in pure C.

## How does Kavy work?

Kavy runs on an event-loop architecture, which works as follows

* The server waits until any clients are readable or writable, checking each one
	* If there is input, the server reads and appends to `client->input`
	* If there's enough data to run commands it does so, appending the output to `client->output`
	* If there is data in `client->output`, the server sends as much as possible, shifting the sent data from the beginning of the output buffer

## What makes Kavy different from Redis?

* Binary protocol - speed is prioritised here, rather than compatibility
* Static hash table with a fixed amount of buckets (1024^2 by default, which takes up only 8MB on a 64-bit system)
* Literally only two features - `set` and `get`

## What's the performance like?

In my synthetic benchmarks, Kavy was up to *10 times faster* than Redis - likely due to it's pure simplicity and lack of logging and client-specific error handling.

## Protocol

All values are big-endian.

### `set`

#### Request
`uint8 1` `uint32 key_length` `uint32 value_length` `char[] key` `char[] value`

#### Response

`uint8 0`
or
`uint8 1` for fail/success respectively

### `get`

#### Request
`uint8 2` `uint32 key_length` `char key[]`

#### Response

`uint8 0`

or

`uint8 2` `uint32 value_length` `char[] value`
