# symmetric-cipher.js
This is a lightweight library meant for symmetric encryption and decryption of strings. It is written for express-js to add support for encrypted cookies, but will also work with Node.js.

This library currently only allows users to use 'AES-256-CBC' symmetric algorithm. On encryption, a random IV is computed and prepended to the ciphertext with `:` as the separator. This library uses the in-built Node.js `crypto` library under the hood.

256-bit (32-byte) keys are required for encryption or decryption. To avoid imposing strict requirements, this library automatically generates a sha256 hash of the key if it is not the required length. However, it is not recommended to use short keys like 'super secret key', though they will still work.

See the [Security section](#security) for tips on generating keys.

## Installation
NPM
```bash
npm install symmetric-cipher.js
```
Yarn
```bash
yarn add symmetric-cipher.js
```

## Compatibility
This library is a commonJS library compatible with Node >= 0.10


## Usage
```javascript
var cipher = require('symmetric-cipher.js');

// Encrypt
cipher.encrypt('my value', 'super secret key');
// rSgTZLgXrFlpAOQkUuTgmQ==:wb/6vFECOGRCSA3DSVLpjw==

// Decrypt
cipher.decrypt('rSgTZLgXrFlpAOQkUuTgmQ==:wb/6vFECOGRCSA3DSVLpjw==', 'super secret key');
// my value
```

ES Modules
```javascript
import cipher from 'symmetric-cipher.js';

// Encrypt
cipher.encrypt('my value', 'super secret key');
// rSgTZLgXrFlpAOQkUuTgmQ==:wb/6vFECOGRCSA3DSVLpjw==

// Decrypt
cipher.decrypt('rSgTZLgXrFlpAOQkUuTgmQ==:wb/6vFECOGRCSA3DSVLpjw==', 'super secret key');
// my value
```
Named imports *may* also work for Node >= 14.13.0.

## Security
The key used for encryption and decryption should be a long, random string. The longer the key, the more secure the encryption. A 32-byte (256-bit) key in this case is sufficient. The key should be stored securely.

### Generating a base64 key (Recommended)
```bash
node
crypto.randomBytes(32).toString('base64')
```
Example: `ktFZQO5JDd4wjwPB2U6bawZKUTGbgOfw6up0wFtjsVk=`

### Generating a hex key
```bash
node
crypto.randomBytes(32).toString('hex')
```
Example: `37f3ce113b34e789ae76ee238c8aa349a6b163e81c283144bffb9c17328360d1`


**NOTE:** For keys generated by the above commands, don't forget to specify the encoding. See the [Examples section](#examples) below.

Store the key securely. For Node.js, you can store it in a `.env` file and use [dotenv](https://github.com/motdotla/dotenv) library to load it into your application. Don't store your key in version control. Add `.env` to `.gitignore`.

## API
### **decodeKey(key, keyEncoding)**
This is a helper function that converts a key to a Buffer object (binary). It leaves Buffer objects intact. It is used internally by the encrypt and decrypt functions.
- `key`: (string | Buffer) The secret used for encryption or decryption. If it is a string, it is converted from the specified encoding to binary.
- `keyEncoding` (string) The encoding of the key. Default is `utf8`. Others include `base64`, `hex`, `ascii`. For full details see [crypto's BufferEncoding](https://nodejs.org/api/buffer.html#buffers-and-character-encodings).
### **encrypt(plainText, key, keyEncoding)**

- `plaintext` (string) The string to encrypt
- `key`: (string | Buffer) The secret used for encryption or decryption. If it is a string, it is converted from the specified encoding to binary.
- `keyEncoding` (string) The encoding of the key. Default is `utf8`. Others include `base64`, `hex`, `ascii`. For full details see [crypto's BufferEncoding](https://nodejs.org/api/buffer.html#buffers-and-character-encodings).

Output is in the format {24-character-iv}:{ciphertext} in base64 encoding.

### **decrypt(cipherText, key, keyEncoding)**
Expected input is in the format {24-character-iv}:{ciphertext} in base64 encoding.

- `cipherText` (string) The cipher to decrypt
- `key`: (string | Buffer) The secret used for encryption or decryption. If it is a string, it is converted from the specified encoding to binary.
- `keyEncoding` (string) The encoding of the key. Default is 'utf8'. Others include 'base64', 'hex', 'ascii'. For full details see [crypto's BufferEncoding](https://nodejs.org/api/buffer.html#buffers-and-character-encodings).

Output is the original plaintext.

## Examples
Base64 key (Recommended)
```javascript
var cipher = require('symmetric-cipher.js');

cipher.encrypt('my value', 'P6wVBCUaAnRlmBNG+1sNV9OY5N9KAyU6TH0ZJuQOmQc=', 'base64')

cipher.decrypt('y3axcNW9eA6ggJNYWotsUQ==:k+YSdJ1MBZct/0bOtQ/zvA==', 'P6wVBCUaAnRlmBNG+1sNV9OY5N9KAyU6TH0ZJuQOmQc=', 'base64')
```

Key as Buffer object (binary)
```javascript
var cipher = require('symmetric-cipher.js');

var key = cipher.decodeKey('P6wVBCUaAnRlmBNG+1sNV9OY5N9KAyU6TH0ZJuQOmQc=', 'base64')

cipher.encrypt('my value', key)

cipher.decrypt('y3axcNW9eA6ggJNYWotsUQ==:k+YSdJ1MBZct/0bOtQ/zvA==', key)
```

Hex key
```javascript
var cipher = require('symmetric-cipher.js');

cipher.encrypt('my value', '3fac1504251a027465981346fb5b0d57d398e4df4a03253a4c7d1926e40e9907', 'hex')

cipher.decrypt('y3axcNW9eA6ggJNYWotsUQ==:k+YSdJ1MBZct/0bOtQ/zvA==', '3fac1504251a027465981346fb5b0d57d398e4df4a03253a4c7d1926e40e9907', 'hex')
```

## License
[MIT](./LICENSE)

## Author
[Fadhili Njagi](https://github.com/FadhiliNjagi)
