# Blowfish cipher

**The libblowfish++ library provides a Blowfish block cipher and a CFB stream cipher for use in C++ projects**  
*A similar library is also available for software development in C [(libblowfish)](https://github.com/raltnoeder/libblowfish)*

## Usage basics

**Encrypting blocks**  
Using the cipher is quite straight-forward:  
```c++
std::string cipher_key("TheSecretKey");

Blowfish cipher;
cipher.set_key(cipher_key);

uint64_t plain_data = ...;
uint64_t cipiher_data = cipher.encrypt(plain_data);
```

**Encrypting data streams**\
The CFB stream cipher is essentially a wrapper around the block cipher:
```c++
std::srting cipher_key("TheSecretKey");

Blowfish cipher;
cipher.set_key(cipher_key);
BlowfishCfb64 stream_cipher(cipher);

uint64_t init_vector = ...;
stream_cipher.set_init_vector(init_vector);

// Set a data block size
// If a continuous stream of data is to be encrypted by calling encrypt()
// multiple times, then data_size must be a multiple of 8 for all blocks,
// except the last one
size_t data_size = ...;
// Create and fill a data buffer with a size of data_size
unsigned char data[] = ...;

// The data is encrypted in-place, that is, the result is placed into
// the 'data' buffer by overwriting the plain text
stream_cipher.encrypt(data, data_size);
```
**Decrypting** works the same way as encrypting, but obviously decrypt()
is called instead of encrypt().

*Notice: For those who look at the source code and are confused by the fact that
the CFB stream cipher's decrypt() method calls the Blowfish block cipher's encrypt()
method: It may be unintuitive, but that's not a bug, it's how CFB mode works.*

**Reusing a cipher instance:**  
```c++
cipher.reinitialize();
cipher.set_key(...);
```

**Cleaning up**  
After setting a key, the cipher's S-boxes and P-boxes can obviously be used
to decrypt anything that has been encrypted by the cipher, so the cipher's
state is just as critical to the system's security as the secret key that
was used to initialize the cipher.
Whenever the cipher is not in use, it is probably a good idea to clear the
cipher's state. This can be done by calling the clear() method:
```c++
cipher.clear()
```
The cipher instance's destructor automatically clears the cipher's state
if it has not been cleared already.

