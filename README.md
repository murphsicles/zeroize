# ⚡ @crypto/zeroize — Secret Memory Zeroization for Zeta

**Port of the Rust `zeroize` crate. Securely clear secrets from memory.
Dead-store-resistant. Compiler-fenced.**

---

## 📦 Package

```
zorbs add @crypto/zeroize
```

## 🔧 API

### Zeroize Trait

```zeta
let mut key: [u8; 32] = [0xAB; 32];
// ... use the key ...
key.zeroize();  // securely overwritten with zeros
```

### Secret Container (auto-zeroizing on drop)

```zeta
let secret_key = Secret::new(my_aes_key);
let key_ref = secret_key.as_ref();
// secret_key is zeroized when it goes out of scope
```

### Zeroizing Newtype

```zeta
fn process_key(key: Zeroizing<[u8; 32]>) {
    let k = key.get();
    // k is zeroized on function return
}
```

### Automatic Zeroize on Drop via `ZeroizeOnDrop`

Implement `Zeroize` on your secret struct, then use `Zeroizing<T>` or
`Secret<T>` to ensure automatic zeroization at scope exit.

## 🧩 Supported Types

| Type | Zeroize impl |
|---|---|
| `i8`, `i16`, `i32`, `i64` | ✅ Volatile write |
| `u8`, `u16`, `u32`, `u64` | ✅ Volatile write |
| `[u8; 32]` | ✅ Byte-level volatile loop |
| `[u8; 64]` | ✅ Byte-level volatile loop |
| `[T]` (slice) | ✅ Element-by-element + compiler fence |
| `Vec<T>` | ✅ Elements + clear + compiler fence |

## 🔒 Security Model

1. **Dead-store resistance**: All zeroize operations use `volatile_write`
   which the compiler cannot optimize away
2. **Compiler fence**: A `compiler_fence()` prevents reordering of
   zeroize operations across the barrier
3. **Double-zeroize protection**: `Secret` and `Zeroizing` track
   whether they've already been zeroized to prevent panics on
   double-drop
4. **No `Copy` on secret types**: `Secret<T>` is not `Copy` —
   accidental duplication must be explicit

## 📄 License

MIT OR Apache-2.0
