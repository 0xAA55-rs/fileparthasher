# File Section Hasher

A Rust library to compute a 64-bit unsigned integer (`u64`) hash for a specific section of a file. Implements the `Write` trait for incremental hashing.

The hash algorithm is `std::hash::DefaultHasher`.

## Overview

Use `FileHasher::new()` to create an instance. Write data into it, and each write operation updates the hash value. Once all data is written, the final `u64` hash is generated.

Key features:
- Hash a specific portion of a file (e.g., skip header/footer).
- Incremental hashing via the `Write` trait.

## Examples

### Example: Hashing a File Section

```rust
use std::fs::File;
use std::io::{Read, Seek};
use fileparthasher::FileHasher;

fn main() -> std::io::Result<()> {
    let mut file = File::open("large_file.bin")?;
    let mut hasher = FileHasher::new();
    
    // Hash bytes 100 to 200 (inclusive)
    let hash = hasher.hash(&mut file, 100, 100)?;
    println!("Hash: {:x}", hash);
    
    Ok(())
}
```

### Example: using `Write` Trait

```rust
use std::io::{Write, Read, Seek};
use fileparthasher::FileHasher;

let mut hasher = FileHasher::new();
hasher.write_all(b"Hello, ")?;
hasher.write_all(b"world!")?;
let hash = hasher.finish(); // Get the hash after all writes
```

## Main Function: `hash()`

```rust
fn hash<R>(mut self, reader: &mut R, from_byte: u64, length: u64) -> io::Result<u64> where R: Read + Seek;
```
* `reader`: A readable and seekable input source (e.g., file).
* `from_byte`: Starting byte offset (0-based).
* `length`: Number of bytes to hash.
* **Returns**: Computed u64 hash or an I/O error.

