r2d2-postgres
=============

[![Build Status](https://travis-ci.org/sfackler/r2d2-postgres.svg?branch=master)](https://travis-ci.org/sfackler/r2d2-postgres)

[rust-postgres](https://github.com/sfackler/rust-postgres) support library for the [r2d2](https://github.com/sfackler/r2d2) connection pool.

Documentation is available at https://sfackler.github.io/r2d2-postgres/doc/v0.9.2/r2d2_postgres

# Example

```rust
extern crate r2d2;
extern crate r2d2_postgres;
extern crate postgres;

use std::sync::Arc;
use std::thread;
use std::default::Default;
use postgres::SslMode;
use r2d2_postgres::PostgresConnectionManager;

fn main() {
    let config = r2d2::Config::default();
    let manager = PostgresConnectionManager::new("postgres://postgres@localhost",
                                                 SslMode::None).unwrap();
    let pool = Arc::new(r2d2::Pool::new(config, manager).unwrap());

    for i in 0..10i32 {
        let pool = pool.clone();
        thread::spawn(move || {
            let conn = pool.get().unwrap();
            conn.execute("INSERT INTO foo (bar) VALUES ($1)", &[&i]).unwrap();
        });
    }
}
```
