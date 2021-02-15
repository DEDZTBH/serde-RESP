# serde-RESP
Redis RESP protocol serialization and deserialization with serde.
[RESP Specification](https://redis.io/topics/protocol)

To use the crate, add this to your `Cargo.toml`:
```
[dependencies]
serde-resp = "0.3.0"
```

## Usage
IMPORTANT: Do NOT serialize and deserialize with any other types besides `RESP`! You may get panic or incorrect results!

Here are the RESP types and their corresponding Rust types for serde.

- `Simple String`
    + `RESP::SimpleString(String)`
- `Error`
    + `RESP::Error(String)`
- `Integer`
    + `RESP::Integer(i64)`
- `Bulk String`
    + `RESP::BulkString(Option<Vec<u8>>)`
        + Use `None` for null bulk strings and `Some` for non-null ones.
- `Array`
    + `RESP::Array(Option<Vec<RESP>>)`
        + Use `None` for null arrays and `Some` for non-null ones.

To serialize, use [ser::to_string](https://docs.rs/serde_resp/0.3.0/serde_resp/ser/fn.to_string.html)
or [ser::to_writer](https://docs.rs/serde_resp/0.3.0/serde_resp/ser/fn.to_writer.html).

To deserialize, use [de::from_str](https://docs.rs/serde_resp/0.3.0/serde_resp/de/fn.from_str.html) 
or [de::from_reader](https://docs.rs/serde_resp/0.3.0/serde_resp/de/fn.from_reader.html)
or [ser::from_buf_reader](https://docs.rs/serde_resp/0.3.0/serde_resp/de/fn.from_buf_reader.html).

For usage examples, refer to https://docs.rs/serde_resp/0.3.0/serde_resp/enum.RESPType.html

## Macros
Since 0.3.0, you can start using very handy macros! Here is a demo:

```rust
use serde_resp::{array, array_null, bulk, bulk_null, de, err_str, int, ser, simple, RESP};
let resp_array = array![
    simple!("simple string".to_owned()),
    err_str!("error string".to_owned()),
    int!(42),
    bulk!(b"bulk string".to_vec()),
    bulk_null!(),
    array![
        simple!("arrays of arrays!".to_owned()),
        array![simple!("OK ENOUGH!".to_owned())],
    ],
    array_null!(),
];
let serialized = ser::to_string(&resp_array).unwrap();
assert_eq!("*7\r\n+simple string\r\n-error string\r\n:42\r\n$11\r\nbulk string\r\n$-1\r\n*2\r\n+arrays of arrays!\r\n*1\r\n+OK ENOUGH!\r\n*-1\r\n", serialized);
let deserialized = de::from_str(&serialized).unwrap();
assert_eq!(resp_array, deserialized);
```

## Documentation

https://docs.rs/serde_resp/0.3.0/serde_resp