# SatoChip APDU Standard

This document provides a complete list of APDU instructions used with SatoChip JavaCard applet.

## 1. Table of Contests

[TOC]

## 2. APDU Specifications

In APDU communication, a psacket is sent to a Java Card applet which processes the request and sends back a response. This is the communication protocol used to communicate with the card using the ISO7816 chip connection or NFC.

### 2.1 Request Format

The request consists of a header and a body. The request is formatted at follows:

| CLA    | INS    | P1     | P2     | $\text{L}_{\text{c}}$     | CDATA          | $\text{L}_{\text{e}}$     |
| ------ | ------ | ------ | ------ | ------------------------- | -------------- | ------------------------- |
| 1 byte | 1 byte | 1 byte | 1 byte | 0, 1 or 3 byte [optional] | var [optional] | 0, 1 or 3 byte [optional] |

**CLA**: The class of which is used to distinguish different products and products classes. For SatoChip, this value is always `0xb0`.

**INS**: The instruction. For a complete list of instructions, check [3 APDU Instructions].

**P1, P2**: parameter 1 and parameter 2 are two values to be passed to the Java Card with the request. For possible values, check the instruction documentation below.

**Lc**: The length of `CDATA`. Can be omitted if the value is `0x00`. The length of this variable can be:

- 0 for no CDATA. 
- 1 for CDATA length between 1 and 255
- 3 for CDATA length between 256 and 65535 where the first byte value is always 0 and the next two bytes encode the rest of the integer in `BIT ENDIAN`

**CDATA**: is an array of bytes of length of 1 to 255.

**Le**: explicit length of response. This can be omitted or sat to value `0x00`. The length of this variable can be:

- 0 for no response. 
- 1 for response length between 1 and 255
- 3 for response length between 256 and 65535 where the first byte value is always 0 and the next two bytes encode the rest of the integer in `BIT ENDIAN`

### 2.2 Response Format

| Response data field | SW1-SW2 |
| ------------------- | ------- |
| var [optional]      | 2 bytes |

**Response data field**: A byte string response from the card.

**SW1-SW2**: Status bytes.

Only valid values for SW1-SW2 are: `0x6XXX` and `0x9XXX` except for `0x60XX` which is invalid.

There are two categories of SW1-SW2 values:

- interindustry: have standard use can can be used by multiple applets
- proprietary: restricted use specified in ISO/IEC 7816-3

| SW1-SW2 | Category      | Comments              |
| ------- | ------------- | --------------------- |
| 61XX    | interindustry |                       |
| 62XX    | interindustry |                       |
| 63XX    | interindustry |                       |
| 63XX    | interindustry |                       |
| 64XX    | interindustry |                       |
| 65XX    | interindustry |                       |
| 66XX    | interindustry |                       |
| 67XX    | proprietary   | 6700 is interindustry |
| 68XX    | interindustry |                       |
| 69XX    | interindustry |                       |
| 6AXX    | interindustry |                       |
| 6BXX    | proprietary   | 6B00 is interindustry |
| 6CXX    | interindustry |                       |
| 6DXX    | proprietary   | 6D00 is interindustry |
| 6EXX    | proprietary   | 6E00 is interindustry |
| 6FXX    | proprietary   | 6F00 is interindustry |
| 9XXX    | proprietary   | 9000 is interindustry |

Some well, known interindustry status bytes include:

- Normal processing
  - `0x9000`: No further qualification
  - `0x61XX`: SW2 encodes the number of data bytes still available
- Warning processing
  - `0x62XX`: State of non-volatile memory is unchanged (further qualification in SW2)
  - `0x63XX`: State of non-volatile memory has changed (further qualification in SW2)
- Execution error
  - `0x64XX`: State of non-volatile memory is unchanged (further qualification in SW2)
  - `0x65XX`: State of non-volatile memory has changed (further qualification in SW2)
  - `0x66XX`: Security-related issues
- Checking error
  - `0x6700`: Wrong length; no further indication
  - `0x68XX`: Functions in CLA not supported (further qualification in SW2)
  - `0x69XX`: Command not allowed (further qualification in SW2)
  - `0x6aXX`: Wrong parameters P1-P2 (further qualification in SW2)
  - `0x6b00`: Wrong parameters P1-P2
  - `0x6cXX`: Wrong Le field; SW2 encodes the exact number of available data bytes
  - `0x6d00`: Instruction code not supported or invalid
  - `0x6e00`: Class not supported 
  - `0x6f00`: No precise diagnosis

**NOTE**: If the process is aborted with a value of SW1 from '64' to '6F', then the response data field shall be absent.

**Note**: If SW1 is set to '63' or '65', then the state of the non-volatile memory has changed. If SW1 is set to '6X' except for '63' and '65', then the state of the non-volatile memory is unchanged.

SW1-SW2 specific to this applet:

- `SW_PIN_FAILED`: `0x63C0`
- `SW_OPERATION_NOT_ALLOWED`: `0x9C03`
- `SW_SETUP_NOT_DONE`: `0x9C04`
- `SW_SETUP_ALREADY_DONE`: `0x9C07`
- `SW_UNSUPPORTED_FEATURE`: `0x9C05`
- `SW_UNAUTHORIZED`: `0x9C06`
- `SW_INCORRECT_ALG`: `0x9C09`
- `SW_NO_MEMORY_LEFT`: `0x9C01`
- `SW_INCORRECT_P1`: `0x9C10`
- `SW_INCORRECT_P2`: `0x9C11`
- `SW_INVALID_PARAMETER`: `0x9C0F`
- `SW_ECKEYS_INITIALIZED_KEY`: `0x9C1A`
- `SW_SIGNATURE_INVALID`: `0x9C0B`
- `SW_IDENTITY_BLOCKED`: `0x9C0C`
- `SW_INTERNAL_ERROR`: `0x9CFF`
- `SW_BIP32_DERIVATION_ERROR`: `0x9C0E`
- `SW_INCORRECT_INITIALIZATION`: `0x9C13`
- `SW_BIP32_UNINITIALIZED_SEED`: `0x9C14`
- `SW_BIP32_INITIALIZED_SEED`: `0x9C17`
- `SW_INCORRECT_TXHASH`: `0x9C15`
- `SW_2FA_INITIALIZED_KEY`: `0x9C18`
- `SW_2FA_UNINITIALIZED_KEY`: `0x9C19`
- `SW_HMAC_UNSUPPORTED_KEYSIZE`: `0x9c1E`
- `SW_HMAC_UNSUPPORTED_MSGSIZE`: `0x9c1F`
- `SW_SECURE_CHANNEL_REQUIRED`: `0x9C20`
- `SW_SECURE_CHANNEL_UNINITIALIZED`: `0x9C21`
- `SW_SECURE_CHANNEL_WRONG_IV`: `0x9C22`
- `SW_SECURE_CHANNEL_WRONG_MAC`: `0x9C23`
- `SW_IMPORTED_DATA_TOO_LONG`: `0x9C32`
- `SW_SECURE_IMPORT_WRONG_MAC`: `0x9C33`
- `SW_SECURE_IMPORT_WRONG_FINGERPRINT`: `0x9C34`
- `SW_SECURE_IMPORT_NO_TRUSTEDPUBKEY`: `0x9C35`
- `SW_PKI_ALREADY_LOCKED`: `0x9C40`
- `SW_RESET_TO_FACTORY`: `0xFF00` (**NOTE**: this is an invalid usage of the status byte as this is considered invalid code)
- `SW_INS_DEPRECATED`: `0x9C26`
- `SW_DEBUG_FLAG`: `0x9FFF`
- 

## 3. APDU Instructions

### 3.1 Instruction `setup`

#### Description

This instruction is supposed to be used one during the initialization of the card.

**INS**: `0x2a`

**P1**: `0x00`

**P2**: `0x00`

**CDATA**:

| name                      | description                                                  | length (bytes) | default value                                       |
| ------------------------- | ------------------------------------------------------------ | -------------- | --------------------------------------------------- |
| `default_pin_length`      | The length of `default_pin` which is by default this byte array: `Muscle00` | 1              | 8                                                   |
| `default_pin`             | The default pin of the card is required to matched during card initialization. The default values comes from the applet source code and it's the byte array `Muscle00` or `{0x4D, 0x75, 0x73, 0x63, 0x6C, 0x65, 0x30, 0x30}` which is the ASCII values of `Muscle00` | var            | `Muscle00`                                          |
| `pin_tries0`              | The number of invalid tries for `pin0`  before the card is locked. This value can be between 1 to 127. But it is recommended to use a lower value between 3-10 for increased security. | 1              | 3-10                                                |
| `ublk_tries0`             | The number of invalid tries for `ublk0`  before the card is locked. This value can be between 1 to 127. But it is recommended to use a lower value between 3-10 for increased security. | 1              | 3-10                                                |
| `pin0_length`             | The length of `pin0`                                         | 1              | 4                                                   |
| `pin0`                    | The main PIN code for the card. This can be a byte array of length between 4-16 (these values from from the applet source code constants `PIN_MIN_SIZE` and `PIN_MAX_SIZE`). This a byte array so make sure the values in here are represented as bytes. For example for a pin code of `0000` the actual byte values would be `{0x30, 0x30, 0x30, 0x30}` where `0x30` is the ASCII code for the number `0`.<br /><br />**Note**: This value is only for testing so make sure you adjust this value in your application and let the user set their own PIN code. | var            | `0000`                                              |
| `ublk0_length`            | The length of `ublk0`                                        | 1              | 6                                                   |
| `ublk0`                   | This is an unblock code for `pin0`. If the user provided an invalid value for `pin0` a number of time equal to `pin_tries0`, the card will not accept `pin0` to access and the unblock code has to be provided. This can be a byte array of length between 4-16 (these values from from the applet source code constants `PIN_MIN_SIZE` and `PIN_MAX_SIZE`). This a byte array so make sure the values in here are represented as bytes. For example for a pin code of `0000` the actual byte values would be `{0x30, 0x30, 0x30, 0x30}` where `0x30` is the ASCII code for the number `0`.<br /><br />**Note**: This value is only for testing so make sure you adjust this value in your application and let the user set their own unblock code or generate a random unblock code during the card initialization and provide it to the user for safe keeping. | var            | `000000`                                            |
| `pin_tries1`              | The number of invalid tries for `pin1`  before the card is locked. This value can be between 1 to 127. But it is recommended to use a lower value between 3-10 for increased security. | 1              | 3-10                                                |
| `ublk_tries1`             | The number of invalid tries for `ublk1`  before the card is locked. This value can be between 1 to 127. But it is recommended to use a lower value between 3-10 for increased security. | 1              | 3-10                                                |
| `pin1_length`             | The length of `pin1`                                         | 1              | 4                                                   |
| `pin1`                    | The main PIN code for the card. This can be a byte array of length between 4-16 (these values from from the applet source code constants `PIN_MIN_SIZE` and `PIN_MAX_SIZE`). This a byte array so make sure the values in here are represented as bytes. For example for a pin code of `0000` the actual byte values would be `{0x30, 0x30, 0x30, 0x30}` where `0x30` is the ASCII code for the number `0`.<br />**Note**: This value is only for testing so make sure you adjust this value in your application and let the user set their own PIN code. | var            | `1234`                                              |
| `ublk1_length`            | The length of `ublk1`                                        | 1              | 6                                                   |
| `ublk1`                   | This is an unblock code for `pin1`. If the user provided an invalid value for `pin0` a number of time equal to `pin_tries1`, the card will not accept `pin0` to access and the unblock code has to be provided. This can be a byte array of length between 4-16 (these values from from the applet source code constants `PIN_MIN_SIZE` and `PIN_MAX_SIZE`). This a byte array so make sure the values in here are represented as bytes. For example for a pin code of `0000` the actual byte values would be `{0x30, 0x30, 0x30, 0x30}` where `0x30` is the ASCII code for the number `0`.<br /><br />**Note**: This value is only for testing so make sure you adjust this value in your application and let the user set their own unblock code or generate a random unblock code during the card initialization and provide it to the user for safe keeping. | var            | `123456`                                            |
| `secmemsize`              | The maximum number of keys and derived keys that can be stored on the card. Each key is 69 bytes. This number should be encoded an `BIG ENDIAN`  over 2 bytes. If the card reaches this maximum their might be unexpected behavior during key derivation. The number should be large enough to facilitate the usage of the card without being too large it takes all the space on the card and doesn't allow normal functions of the card to be performed. There is a function in the source fore to destroy keys that could be used for old keys, but it is not used.<br /><br />**Note**: This value represents a `signed int16` but acceptable values should be between 1 to 2,147,483,647 without using the negative values. But in practice, using very large values like 2,147,483,647 will not work on any device because most Java Cards do not have that much memory. For example, a J3R180 has 196 kbytes of EEPROM. a value for `secmemsize` of 1,000 means you are using 69 x 1000 = 69,000 bytes (~67.4 kbytes) which consumes ~34% of the cards EEPROM. So, a good practice is to test for the kind of hardware you have and review the hardware's data sheets to learn about the amount of EPPROM available in your choice of hardware. | 2 (BIG ENDIAN) | 500 which is encoded in BIG ENDIAN as`{0x01, 0xf4}` |
| `RFU`                     | Reserved for future use                                      | 2              | `{0x00, 0x00}`                                      |
| `RFU`                     | Reserved for future use                                      | 3              | `{0x00, 0x00, 0x00}`                                |
| `option_flags` [optional] | 2 bytes of bit flags (16 flags) for additional features. This is the list of features and their values:<br /> - `HMAC_CHALRESP_2FA` (`0x8000`): enables 2-factor authentication (2FA) using hmac-sha1 challenge-response. To enable this feature, set the corresponding bit value to 1 which makes the value `0bxxxx1xxx, 0bxxxxxxxx ` where `x` can be `0` or `1` depending of the need to enable other additional features. | 2              | `{0x00, 0x00}`                                      |
| `hmacsha1_key` [optional] | HMAC key used for transaction authorization when 2FA is enabled. | 20             | NA                                                  |
| `amount_limit` [optional] | Max amount (in satoshis) allowed without confirmation (this includes change value) when 2FA is enabled. This value is is a `uint64` encoded in 8 bytes in `BIG ENDIAN` or `LITTLE ENDIAN`?<br /><br />**Note**: It seems there is a bug in the implementation for checking the amount. The amount in the transaction is encoded as `LITTLE ENDIAN` but he `BigInteger.lessThan` function used uses `BIG_ENDIAN` which makes this feature impossible to use. | 8              | NA                                                  |

#### Request Examples

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x1a, 0x00, 0x00, 0x2c, 
   0x08, // default_pin_length
   0x4d, 0x75, 0x73, 0x63, 0x6c, 0x65, 0x30, 0x30, // default_pin
   0x03, // pin_tries0
   0x03, // ublk_tries0
   0x04, // pin0_length
   0x30, 0x30, 0x30, 0x30, // pin0
   0x06, // ublk0_length
   0x30, 0x30, 0x30, 0x30, 0x30, 0x30, // ublk0
   0x03, // pin_tries1
   0x03, // ublk_tries1
   0x04, // pin1_length
   0x30, 0x31, 0x32, 0x33, // pin1
   0x06, // ublk1_length
   0x30, 0x31, 0x32, 0x33, 0x34, 0x35, // ublk1
   0x01, 0xf4, // secmemsize
   0x00, 0x00, // RFU
   0x00, 0x00, 0x00, // RFU
   0x00, 0x00 // option_flags
}
```

```bash
$ gp -a b02a00002c084d7573636c65303003030430303030063030303030300303043031323306303132333435000a0000000000
...
A>> T=1 (4+0044) B02A0000 2C 084D7573636C65303003030430303030063030303030300303043031323306303132333435000A0000000000
A<< (0000+2) (376ms) 9000
...
```

This response can be parsed in JSON as:

```json
{
	"statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification"
}
```

#### Response

`None`

### 3.2 Instruction `importKey`

#### Description

This function allows the import of a private ECkey into the card.

**INS**: `0x32`

**P1**: private key number `0x00` to `0x0f` (NOTE: if there is already a key at the specified number the card will return `SW_INCORRECT_P1` (`0x9c10`))

**P2**: `0x00`

**CDATA**:

| name                  | description                                                  | length (bytes) | default value                          |
| --------------------- | ------------------------------------------------------------ | -------------- | -------------------------------------- |
| `key_encoding`        | `0x00` is `BLOB_ENC_PLAIN` which is the only supported value | 1              | `0x00`                                 |
| `key_type`            | `0x0c` is `TYPE_EC_FP_PRIVATE` which is the only supported value | 1              | `0x0c`                                 |
| `key_size`            | `0x0100` is `LENGTH_EC_FP_256` which is the only supported value | 2              | `{0x01, 0x00}` which is 256            |
| `RFU`                 | Reserved for future use                                      | 6              | `{0x00, 0x00, 0x00, 0x00, 0x00, 0x00}` |
| `key_blob`            | The first two bytes are the `blob_size` encoded in `BIG ENDIAN`.  The which should be 32 or `{0x00, 0x20}`. The remaining 32 bytes should include the bytes of private key. | 34             | NA                                     |
| `HMAC-2FA` [optional] | 20 bytes HMAC key for 2FA.                                   | 20             | NA                                     |

#### Request Examples

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x32, 0x00, 0x00, 0x2c, 
   0x00, // key_encoding
   0x0c, // key_type
   0x01, 0x00, // key_size
   0x00, 0x00, 0x00, 0x00, 0x00, 0x00, //RFU
   0x00, 0x20, ... // key_blob
}
```

```bash
$ gp -a b03200002c000c01000000000000000020
```

#### Response

None

### 3.3 Instruction `resetKey`

#### Description

This function allows to reset a private ECkey stored in the card. If 2FA is enabled, a hmac code must be provided to reset the key.

**INS**: `0x33`

**P1**: private key number `0x00` to `0x0f` (NOTE: if there is no key at the specified number the card will return `SW_INCORRECT_P1` (`0x9c10`))

**P2**: `0x00`

**CDATA**:

| name                  | description                | length (bytes) | default value |
| --------------------- | -------------------------- | -------------- | ------------- |
| `HMAC-2FA` [optional] | 20 bytes HMAC key for 2FA. | 20             | NA            |

#### Request Examples

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x33, 0x00, 0x00, 0x00
}
```

```bash
$ gp -a b033000000
```

#### Response

None

### 3.4 Instruction `getPublicKeyFromPrivate`

#### Description

This function returns the public key associated with a particular private key stored in the applet. The exact key blob contents depend on the key algorithm and type.

**INS**: `0x35`

**P1**: private key number `0x00` to `0x0f` (NOTE: if there is no key at the specified number the card will return `SW_INCORRECT_P1` (`0x9c10`))

**P2**: `0x00`

**CDATA**: None

#### Request Examples

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x35, 0x00, 0x00, 0x00
}
```

#### Response

TODO

### 3.5 Instruction `createPIN`

#### Description

This function creates a PIN with parameters specified by the P1, P2 and DATA values. P2 specifies the maximum number of consecutive unsuccessful verifications before the PIN blocks. PIN can be created only if one of the logged identities allows it.

**INS**: `0x40`

**P1**: PIN number `0x00`-`0x07` (NOTE: if there is a PIN already at the specified number the card will return `SW_INCORRECT_P1` (`0x9c10`))

**P2**: max attempt number `0x01`-`0x7f` (1-127)

**CDATA**:

| name        | description                                                  | length (bytes) | default value                          |
| ----------- | ------------------------------------------------------------ | -------------- | -------------------------------------- |
| `PIN_size`  | length of `PIN`                                              | 1              | `0x04`                                 |
| `PIN`       | The desired pin in byte array format. For a PIN of `0000` you should use `{0x30, 0x30, 0x30, 0x30}` | var            | `{0x30, 0x30, 0x30, 0x30}`             |
| `UBLK_size` | length of `UBLK`                                             | 1              | `0x06`                                 |
| `UBLK`      | The desired unblock code for the newly created PIN in byte array format. For a an unlock code of of `0000` you should use `{0x30, 0x30, 0x30, 0x30}` | var            | `{0x30, 0x30, 0x30, 0x30, 0x30, 0x30}` |

#### Request Examples

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x40, 0x00, 0x00, 0x0c,
   0x04, // PIN_size
   0x30, 0x30, 0x30, 0x30, // PIN
   0x06, // UBLK_size
   0x30, 0x30, 0x30, 0x30, 0x30, 0x30 // UBLK_size
}
```

#### Response

TODO

### 3.6 Instruction `verifyPIN`

#### Description

This function verifies a PIN number sent by the DATA portion. The length of this PIN is specified by the value contained in `LC`. Multiple consecutive unsuccessful PIN verifications will block the PIN. If a PIN blocks, then an UnblockPIN command can be issued.

**INS**: `0x42`

**P1**: PIN number `0x00`-`0x07` (NOTE: if there is no PIN at the specified number the card will return `SW_INCORRECT_P1` (`0x9c10`))

**P2**: `0x00`

**CDATA**:

| name  | description                                                  | length (bytes) | default value              |
| ----- | ------------------------------------------------------------ | -------------- | -------------------------- |
| `PIN` | The pin in byte array format. For a PIN of `0000` you should use `{0x30, 0x30, 0x30, 0x30}` | var            | `{0x30, 0x30, 0x30, 0x30}` |

#### Request Examples

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x42, 0x00, 0x00, 0x04,
   0x30, 0x30, 0x30, 0x30, // PIN
}
```

```bash
$ gp -a b04200000430303030
...
A>> T=1 (4+0004) B0420000 04 30303030
A<< (0000+2) (26ms) 9000
...
```

This response can be parsed in JSON as:

```json
{
	"statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification"
}
```

#### Response

`None`

### 3.7 Instruction `changePIN`

#### Description

This function changes a PIN code. The DATA portion contains both the old and the new PIN codes.

**INS**: `0x44`

**P1**: PIN number `0x00`-`0x07` (NOTE: if there is no PIN at the specified number the card will return `SW_INCORRECT_P1` (`0x9c10`))

**P2**: `0x00`

**CDATA**:

| name       | description                                                  | length (bytes) | default value              |
| ---------- | ------------------------------------------------------------ | -------------- | -------------------------- |
| `PIN_size` | length of `old_PIN`                                          | 1              | `0x04`                     |
| `old_PIN`  | The old pin in byte array format. For a PIN of `0000` you should use `{0x30, 0x30, 0x30, 0x30}` | var            | `{0x30, 0x30, 0x30, 0x30}` |
| `PIN_size` | length of `new_PIN`                                          | 1              | `0x04`                     |
| `new_PIN`  | The new pin in byte array format. For a PIN of `0000` you should use `{0x30, 0x30, 0x30, 0x30}` | var            | `{0x31, 0x31, 0x31, 0x31}` |

#### Request Examples

This example changes the PIN 0 from `0000` to `1111`

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x44, 0x00, 0x00, 0x0a,
   0x04, // PIN_size
   0x30, 0x30, 0x30, 0x30, // old_PIN
   0x04, // PIN_size
   0x31, 0x31, 0x31, 0x31, // new_PIN
}
```

```bash
$ gp -a b04400000a04303030300431313131
...
A>> T=1 (4+0010) B0440000 0A 04303030300431313131
A<< (0000+2) (28ms) 9000
...
```

This response can be parsed in JSON as:

```json
{
	"statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification"
}
```

#### Response

`None`

### 3.8 Instruction `unblockPIN`

#### Description

This function unblocks a PIN number using the unblock code specified in the DATA portion. The `LC` byte specifies the unblock code length. 

**INS**: `0x46`

**P1**: PIN number `0x00`-`0x07` (NOTE: if there is no PIN at the specified number the card will return `SW_INCORRECT_P1`)

**P2**: `0x00`

**CDATA**:

| name  | description                                                  | length (bytes) | default value                          |
| ----- | ------------------------------------------------------------ | -------------- | -------------------------------------- |
| `PUK` | The unblock code in byte array format. For a unblock code of `0000` you should use `{0x30, 0x30, 0x30, 0x30}` | var            | `{0x30, 0x30, 0x30, 0x30, 0x30, 0x30}` |

#### Request Examples

This example unblocks the PIN 0 using unblock code `000000`.

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x46, 0x00, 0x00, 0x06,
   0x30, 0x30, 0x30, 0x30, 0x30, 0x30, // PUK
}
```

```bash
$ gp -a b046000006303030303030
```

#### Response

TODO

### 3.9 Instruction `listPINs`

#### Description

This function returns a 2 byte bit mask of the available PINs that are currently in use. Each set bit corresponds to an active PIN.

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` (`0x9c06`) error.

**INS**: `0x48`

**P1**: `0x00`

**P2**: `0x00`

**CDATA**: None

#### Request Examples

This example requests a list of all available PINs

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x48, 0x00, 0x00, 0x02,
   0x00, 0x00
}
```

```bash
$ gp -a b04200000430303030 -a b048000002000002
...
A>> T=1 (4+0004) B0420000 04 30303030
A<< (0000+2) (27ms) 9000
A>> T=1 (4+0002) B0480000 02 0000 02
A<< (0002+2) (7ms) 0003 9000
...
```

This response can be parsed in JSON as:

```json
{
    "_comment": "response for verifyPIN",
    "statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification",
    
    "_comment": "response for listPINs",
    "RFU": "00",
    "PIN_mask": "03",
    "statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification"
}
```

#### Response

| Name       | Description             | Length (bytes) |
| ---------- | ----------------------- | -------------- |
| `RFU `     | Reserved for future use | 1              |
| `PIN_mask` | Bit mask of in-use PIN  | 1              |

### 3.10 Instruction `getStatus`

#### Description

This function retrieves general information about the Applet running on the smart card, and useful information about the status of current session such as:

- `protocolVersion` (2 bytes): `{PROTOCOL_MAJOR_VERSION, PROTOCOL_MINOR_VERSION}`
- `appletVersion` (2 bytes): `{APPLET_MAJOR_VERSION, APPLET_MINOR_VERSION}`
- `pinRemainingTries0` (1 byte)
- `ublkRemainingTries0` (1 byte)
- `pinRemainingTries1` (1 byte)
- `ublkRemainingTries1` (1 byte)
- `needs2FA` (1 byte): `0x00` for false or `0x01` for true
- `is_seeded` (1 byte): check if BIP32 was seeded and returns `0x00` for false or `0x01` for true
- `setupDone` (1 byte): `0x00` for false or `0x01` for true
- `needs_secure_channel` (1 byte): `0x00` for false or `0x01` for true

**INS**: `0x3c`

**P1**: `0x00`

**P2**: `0x00`

**CDATA**: None

#### Request Examples

This example requests the status of a card

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x3c, 0x00, 0x00, 0x00
}
```

```bash
$ gp -a b03c000000 -d
...
A>> T=1 (4+0000) B03C0000 00
A<< (0012+2) (15ms) 000C00060000000000000001 9000
...
```

This response can be parsed in JSON as:

```json
{
	"protocolVersion": "000C",
    "appletVersion": "0006",
    "pinRemainingTries0": "00",
    "ublkRemainingTries0": "00",
    "pinRemainingTries1": "00",
    "ublkRemainingTries1": "00",
    "needs2FA": "00",
    "is_seeded": "00",
    "setupDone": "00",
    "needs_secure_channel": "01",
    "statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification"
}
```



#### Response

| name                   | description                                                  | length (bytes) |
| ---------------------- | ------------------------------------------------------------ | -------------- |
| `protocolVersion`      | `{PROTOCOL_MAJOR_VERSION, PROTOCOL_MINOR_VERSION}`           | 2              |
| `appletVersion`        | `{APPLET_MAJOR_VERSION, APPLET_MINOR_VERSION}`               | 2              |
| `pinRemainingTries0`   | The remaining tries for `PIN0`                               | 1              |
| `ublkRemainingTries0`  | The remaining tries for `UBLK0`                              | 1              |
| `pinRemainingTries1`   | The remaining tries for `PIN1`                               | 1              |
| `ublkRemainingTries1`  | The remaining tries for `UBLK1`                              | 1              |
| `is_seeded`            | check if BIP32 was seeded and returns `0x00` for false or `0x01` for true | 1              |
| `setupDone`            | `0x00` for false or `0x01` for true                          | 1              |
| `needs_secure_channel` | `0x00` for false or `0x01` for true                          | 1              |



### 3.11 Instruction `cardLabel`

#### Description

This function allows to define or recover a short description of the card.

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` (`0x9c06`) error.

**INS**: `0x3d`

**P1**: `0x00`

**P2**: operation (`0x00` to set label, `0x01` to get label)

**CDATA**:

| name                                     | description                                                  | length (bytes) | default value |
| ---------------------------------------- | ------------------------------------------------------------ | -------------- | ------------- |
| `label_size` [required for  `P2`=`0x00`] | length of `label`                                            | 1              | NA            |
| `label` [required for  `P2`=`0x00`]      | short description of the card to be used as a label. Can be useful to distinguish different cards. This is formatted as a byte array. | var            | NA            |

#### Request Examples

This example sets the label of the card as `test card`

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x3d, 0x00, 0x00, 0x0a,
   0x09, // label_size
   0x74, 0x65, 0x73, 0x74, 0x20, 0x63, 0x61, 0x72, 0x64, // label
}
```

```bash
$ gp -a b04200000430303030 -a b03d00000a09746573742063617264 -d
...
A>> T=1 (4+0004) B0420000 04 30303030
A<< (0000+2) (26ms) 9000
A>> T=1 (4+0010) B03D0000 0A 09746573742063617264
A<< (0000+2) (12ms) 9000
...
```

This response can be parsed in JSON as:

```json
{
    "_comment": "response for verifyPIN",
    "statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification",
    
    "_comment": "response for cardLabel",
    "statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification"
}
```

#### Response

`None`

### 3.12 Instruction `importBIP32Seed`

#### Description

This function imports a Bip32 seed to the applet and derives the master key and chain code. It also derives a second ECC that uniquely authenticates the HDwallet: the authentikey. Lastly, it derives a 32-bit AES key that is used to encrypt/decrypt Bip32 object stored in secure memory.

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` (`0x9c06`) error.

**Note**: If the card has already been seeded with BIP32, you will get back an error `SW_BIP32_INITIALIZED_SEED` (`0x9c17`)

**INS**: `0x6c`

**P1**: seed_size (1 byte). This value should be between 0-64 otherwise the card throws an error `SW_WRONG_LENGTH` (`0x6700`)

**P2**: `0x00`

**CDATA**:

| name        | description                                                  | length (bytes) | default value |
| ----------- | ------------------------------------------------------------ | -------------- | ------------- |
| `seed_data` | The BIP32 seed which is usually 39 bytes. This is not to be confused with BIP39 mnemonic phrase or its entropy. If you are starting with a mnemonic phrase, first derive a BIP32 seed from the entropy and send the derived seed. | var            | NA            |

#### Request Examples

This example imports a BIP32 seed into the card. This key was derived from the following pass phrase:

```
abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon diesel
```

The key is:

```
2f5f7bb39a1c678b0c3daba144ac0a1cb17227198acd5bf7eb7252a2b86e79e335541b09a77615
```

Note: we used http://bip32.org/ to derive this key.

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x6c, 0x00, 0x00, 0x27,
   0x54, 0xed, 0x02 ,0xf3 ... // seed_data (64 bytes)
}
```

```bash
gp -a b04200000430303030 -a b06c0000272f5f7bb39a1c678b0c3daba144ac0a1cb17227198acd5bf7eb7252a2b86e79e335541b09a77615
...
A>> T=1 (4+0004) B0420000 04 30303030
A<< (0000+2) (27ms) 9000
A>> T=1 (4+0039) B06C0000 27 2F5F7BB39A1C678B0C3DABA144AC0A1CB17227198ACD5BF7EB7252A2B86E79E335541B09A77615
A<< (0107+2) (85ms) 00202716118B3DFB39DE8FD05B257AF1D3E9BD80C18753248CE7DC1D8E2B1E4C57EB0048304602210081E7E6845C803D345A92C34AB5825408FD301B756F2C67BCE43AAFFB83028F68022100C2D446A0B8CD581B56394EB60150E858E8167E438B45489D3FCE55F7862D70E0 9000
...
```

This response can be parsed in JSON as:

```json
{
    "_comment": "response for verifyPIN",
    "statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification",
    
    "_comment": "response for importBIP32Seed",
	"coordx_size": "0020",
    "coordx": "2716118B3DFB39DE8FD05B257AF1D3E9BD80C18753248CE7DC1D8E2B1E4C57EB",
    "sig_size": "0048",
    "sig": "304602210081E7E6845C803D345A92C34AB5825408FD301B756F2C67BCE43AAFFB83028F68022100C2D446A0B8CD581B56394EB60150E858E8167E438B45489D3FCE55F7862D70E0",
    "statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification"
}
```

#### Response

| Name          | Description                      | Length (bytes) |
| ------------- | -------------------------------- | -------------- |
| `coordx_size` | size of `x-coordinate`           | 2              |
| `coordx`      | `x-coordinate` of the public key |                |
| `sig_size`    | size of `sig`                    | 2              |
| `sig`         | self-signed `x-coordinate`       |                |

### 3.13 Instruction `resetBIP32Seed`

#### Description

This function resets the Bip32 seed and all derived keys: the master key, chain code, authentikey and the 32-bit AES key that is used to encrypt/decrypt Bip32 object stored in secure memory. If 2FA is enabled, then a HMAC code must be provided, based on the 4-byte counter-2FA.

**Note**: If the card has already been seeded with BIP32, you will get back an error `SW_BIP32_INITIALIZED_SEED` (`0x9c17`)

**INS**: `0x77`

**P1**: PIN_size (1 byte). This value should indicate the length of `PIN` in CDATA.

**P2**: `0x00`

**CDATA**:

| name                       | description                                                  | length (bytes) | default value              |
| -------------------------- | ------------------------------------------------------------ | -------------- | -------------------------- |
| `PIN`                      | The pin in byte array format. For a PIN of `0000` you should use `{0x30, 0x30, 0x30, 0x30}` | var            | `{0x30, 0x30, 0x30, 0x30}` |
| `optional-hmac` [optional] | 20 bytes HMAC key for 2FA.                                   | 20             | NA                         |

#### Request Examples

This example resets the BIP32 seed on the card

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x77, 0x04, 0x00, 0x04,
   0x30, 0x30, 0x30, 0x30 // PIN
}
```

```bash
$ gp -a b07704000430303030
...
A>> T=1 (4+0004) B0770400 04 30303030
A<< (0000+2) (38ms) 9000
...
```

This response can be parsed in JSON as:

```json
{
    "statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification"
}
```

#### Response

`None`

### 3.14 Instruction `getBIP32AuthentiKey`

#### Description

This function returns the authentikey public key (uniquely derived from the Bip32 seed). The function returns the x-coordinate of the authentikey, self-signed. The authentikey full public key can be recovered from the signature.

**NOTE**: It doesn't seem like authentikey is actually "uniquely derived from the Bip32 seed". This has to be tested separately by getting the key using INS `getAuthentiKey` then import a BIP32 key then get the key again using this instruction and compare the two results. If the results are the same, then the authentikey is not "uniquely derived from the Bip32 seed" but just randomly generated during installation.

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` (`0x9c06`) error.

**INS**: `0x73`

**P1**: `0x00`

**P2**: `0x00`

**CDATA**: None

#### Request Examples

This example requests the authntikey public key and it's signature.

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x73, 0x00, 0x00, 0x00
}
```

```bash
$ gp -a b04200000430303030 -a b073000000
...
A>> T=1 (4+0004) B0420000 04 30303030
A<< (0000+2) (27ms) 9000
A>> T=1 (4+0000) B0730000 00 
A<< (0106+2) (51ms) 00202716118B3DFB39DE8FD05B257AF1D3E9BD80C18753248CE7DC1D8E2B1E4C57EB00463044022041785BCFF62DC4840DAD892074226C401F01AA50DA1BB1A493EEF96BD27BF0E1022046BBA1CADCF81BB4A7BA4707CAF8DD07283FCD2633176250C0F60219FC0B3E93 9000
...
```

This response can be parsed in JSON as:

```json
{
    "_comment": "response for verifyPIN",
    "statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification",
    
    "_comment": "response for getBIP32AuthentiKey",
	"coordx_size": "0020",
    "coordx": "2716118B3DFB39DE8FD05B257AF1D3E9BD80C18753248CE7DC1D8E2B1E4C57EB",
    "sig_size": "0046",
    "sig": "3044022041785bcff62dc4840dad892074226c401f01aa50da1bb1a493eef96bd27bf0e1022046bba1cadcf81bb4a7ba4707caf8dd07283fcd2633176250c0f60219fc0b3e93",
    "statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification"
}
```

#### Response

| Name          | Description                      | Length (bytes) |
| ------------- | -------------------------------- | -------------- |
| `coordx_size` | size of `x-coordinate`           | 2              |
| `coordx`      | `x-coordinate` of the public key |                |
| `sig_size`    | size of `sig`                    | 2              |
| `sig`         | self-signed `x-coordinate`       |                |

### 3.15 Instruction `getAuthentiKey`

#### Description

This function returns the authentikey public key. The function returns the x-coordinate of the authentikey, self-signed. The authentikey full public key can be recovered from the signature.

Compared to `getBIP32AuthentiKey`, this method returns the Authentikey even if the card is not seeded. For SeedKeeper encrypted seed import, we use the authentikey as a Trusted Pubkey for the ECDH key exchange, thus the authentikey must be available before the Satochip is seeded. Before a seed is available, the authentiey is generated oncard randomly in the constructor.

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` (`0x9c06`) error.

**INS**: `0xad`

**P1**: `0x00`

**P2**: `0x00`

**CDATA**: None

#### Request Examples

This example requests the authntikey public key and it's signature.

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0xad, 0x00, 0x00, 0x00
}
```

```bash
$ gp -a b04200000430303030 -a b0ad000000
...
A>> T=1 (4+0004) B0420000 04 30303030
A<< (0000+2) (26ms) 9000
A>> T=1 (4+0000) B0AD0000 00 
A<< (0106+2) (51ms) 00202716118B3DFB39DE8FD05B257AF1D3E9BD80C18753248CE7DC1D8E2B1E4C57EB00463044022075F8101557E5384CEFE6CA4DDB4A40DFBAE86D69F775437ACD0AC7A29744274902204F0E226629FEB1E59EA0F8CEFC5076545651BA5BDAE8EB6D79613F3F002304A8 9000
...
```

This response can be parsed in JSON as:

```json
{
    "_comment": "response for verifyPIN",
    "statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification",
    
    "_comment": "response for getBIP32AuthentiKey",
	"coordx_size": "0020",
    "coordx": "2716118B3DFB39DE8FD05B257AF1D3E9BD80C18753248CE7DC1D8E2B1E4C57EB",
    "sig_size": "0046",
    "sig": "3044022075F8101557E5384CEFE6CA4DDB4A40DFBAE86D69F775437ACD0AC7A29744274902204F0E226629FEB1E59EA0F8CEFC5076545651BA5BDAE8EB6D79613F3F002304A8",
    "statusBytes": "9000",
    "statusBytesMsg": "Normal: No further qualification"
}
```

#### Response

| Name          | Description                      | Length (bytes) |
| ------------- | -------------------------------- | -------------- |
| `coordx_size` | size of `x-coordinate`           | 2              |
| `coordx`      | `x-coordinate` of the public key |                |
| `sig_size`    | size of `sig`                    | 2              |
| `sig`         | self-signed `x-coordinate`       |                |

### 3.16 Instruction `setBIP32AuthentikeyPubkey` [DEPRECATED]

There is no actual implementation in the applet for this instruction.

### 3.17 Instruction `getBIP32ExtendedKey`

#### Description

The function computes the Bip32 extended key derived from the master key and returns the x-coordinate of the public key signed by the authentikey. Extended key is stored in the chip in a temporary EC key, along with corresponding ACL. Extended key and chaincode is also cached as a Bip32 object in secure memory.

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` (`0x9c06`) error.

**Note**: If the card has not already been seeded with BIP32, you will get back an error `SW_BIP32_UNINITIALIZED_SEED` (`0x9C14`)

**INS**: `0x6d`

**P1**: depth of the extended key (master is depth 0, m/i is depht 1). Max depth is 10

**P2**: 8 flags for different options during this operation. Currently the only option implemented is (`0x80` or `0b1000 0000`) which resets all stored BIP32 stored keys before derive the new key.

**CDATA**:

| name         | description                                                  | length (bytes) | default value |
| ------------ | ------------------------------------------------------------ | -------------- | ------------- |
| `index_path` | `index_path` from master to extended key (m/i/j/k/...). 4 bytes per index. For example, to get a BIP44 extended key for `M / 44' / 0' / 0' / 0 / 0` the value would consist of the following byte blocks:<br /> - `0x8000002c` where `0x80000000` denotes the single bit for a hardened key and `0x0000002c` is 44 in hex.<br /> - `0x80000000`<br /> - `0x80000000`<br /> - `0x00000000`<br /> - `0x00000000`<br />concatenated these byte blocks gives this: `0x8000002c80000000800000000000000000000000` | var            | NA            |

#### Request Examples

This example requests the extended public key for HD wallet path `M / 44' / 0' / 0' / 0 / 0`

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x6d, 0x00, 0x00, 0x14,
   0x80, 0x00, 0x00, 0x2c, 0x80, 0x00, 0x00, // ==========
   0x00, 0x80, 0x00, 0x00, 0x00, 0x00, 0x00, // index_path
   0x00, 0x00, 0x00, 0x00, 0x00, 0x00        // ==========
}
```

#### Response

TODO

### 3.18 Instruction `setBIP32ExtendedPubkey` [DEPRECATED]

There is no actual implementation in the applet for this instruction.

### 3.19 Instruction `signMessage`

#### Description

This function signs Bitcoin message using std or Bip32 extended key

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` (`0x9c06`) error.

**Note**: If the card has not already been seeded with BIP32, you will get back an error `SW_BIP32_UNINITIALIZED_SEED` (`0x9C14`).

**INS**: `0x6e`

**P1**: key number or 0xFF for the last derived Bip32 extended key

**P2**: acceptable values are `0x01`=`Init`, `0x02`=`Update`,  `0x03`=`Finalize`. To actually sign a message, `Init` process has to be performed and to receive the result, a `Finalize` process has to be performed. `Update` process is only required is the message to too long to fit into a single APDU request.

**Note**: If an `Update` or a `Finalize` process was performed before `Init` process, the card will return an error `SW_INCORRECT_INITIALIZATION` (`0x9C13`)

**CDATA**:

| name                                       | description                                                  | length (bytes) | default value |
| ------------------------------------------ | ------------------------------------------------------------ | -------------- | ------------- |
| `full_msg_size` (`Init` process)           | the full size of the message to be signed as a `signed int16` encoded as `BIG ENDIAN` byte array | 4              | NA            |
| `altcoinSize` (`Init` process) [optional]  | The size on `altcoin`                                        | 1              | NA            |
| `altcoin` (`Init` process) [optional]      | Optional byte array for the alt coin                         | var            | NA            |
| `chunk_size` (`Update`/`Finalize` process) | the size of `chunk_data`                                     | 2              | NA            |
| `chunk_data` (`Update`/`Finalize` process) | a byte array containing the message to be signed.            | var            | NA            |
| `HMAC-2FA` (`Finalize` process) [optional] | 20 bytes HMAC key for 2FA.                                   | 20             | NA            |

#### Request Examples

This example signs a message `hello world` using latest derived key using two requests to perform `Init` then `Finalize` processes.

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x6e, 0xff, 0x01, 0x04,
   0x00, 0x00, 0x00, 0x0b // full_msg_size
}
```

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x6e, 0xff, 0x03, 0x0c,
   0x0b, // chunk_size
   0x68, 0x65, 0x6c, 0x6c, 0x6f, 0x20, 0x77, 0x6f, 0x72, 0x6c, 0x64 // chunk_data
}
```

#### Response

TODO

### 3.20 Instruction `parseTransaction`

#### Description

This function parses a raw transaction and returns the corresponding double SHA-256. If the Bip32 seed is initialized, the hash is signed with the authentikey.

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` (`0x9c06`) error.

**INS**: `0x71`

**P1**: `Init` = `0x01` or `Process`=`0x02`

**P2**: `PARSE_STD` = `0x00` or `PARSE_SEGWIT` = `0x01`

**CDATA**:

| name     | description                                | length (bytes) | default value |
| -------- | ------------------------------------------ | -------------- | ------------- |
| `raw_tx` | byte array of the transaction to be parsed | var            | NA            |

#### Request Examples

This example is a request to parse the following transaction which is of length 226 (`0xe2`). Since this transaction is not longer than 255 bytes, `P1` for this request is `Init` (or `0x01`).

```
020000000180b98c54dbab5106d5a1449f4e5fdb9146deca1d48e93d666c5d9290b7c37a3f010000006b483045022100f0e32ceb205a5056694611afcffe4c1f0e63e9c57382607045ff2c3d9b5b7b3f0220111f0323e56d7462a9299833166569f1a68e1f5090b49bea64f541c494109c6c012102d0648f06a31d47112f1ff7848c85ce54b772c513bc3337c98f081c19d3dca260ffffffff02006d7c4d000000001976a91474d463a046e3175142464740bad692fa0762a93e88accad5e5f1b50000001976a914c98fc6bd9c2fd88533f28e6797cfa2a0a0e18ecf88ac00000000
```

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x71, 0x00, 0x00, 0xe2,
   0x02, 0x00, 0x00, 0x00, 0x01, //
   0x80, 0xb9, 0x8c ...          // raw_tx
}
```

#### Response

TODO

### 3.21 Instruction `signTransaction`

#### Description

This function signs the current hash transaction with a std or the last extended key. The hash provided in the APDU is compared to the version stored inside the chip. Depending of the total amount in the transaction and the predefined limit, a HMAC must be provided as an additional security layer.

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` (`0x9c06`) error.

**INS**: `0x6f`

**P1**: key number or `0xff` for the last derived Bip32 extended key

**P2**: `0x00`

**CDATA**:

| name      | description                                                  | length (bytes) | default value |
| --------- | ------------------------------------------------------------ | -------------- | ------------- |
| `tx_hash` | the hash of the transaction as calculated or received from `parseTransaction` | 32             | NA            |

#### Request Examples

This example is a request sign a transaction with the latest derived BIP32 key. The transaction was preciously parsed using `parseTransaction` with the transaction hash `a637ad18fabee7ad3ccd51e317091a6e16991311c0c9b83233b140b66b114448`

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x6f, 0x00, 0x00, 0x20,
   0xa6, 0x37, 0xad, 0x18, 0xfa, 0xbe, 0xe7, //
   0xad, 0x3c, 0xcd, 0x51, 0xe3, 0x17, 0x09, //
   0x1a, 0x6e, 0x16, 0x99, 0x13, 0x11, 0xc0, //
   0xc9, 0xb8, 0x32, 0x33, 0xb1, 0x40, 0xb6, // 
   0x6b, 0x11, 0x44, 0x48                    // tx_hash
}
```

#### Response

TODO

### 3.22 Instruction `signTransactionHash`

#### Description

This function signs a given transaction hash with a std or the last extended key. If 2FA is enabled, a HMAC must be provided as an additional security layer. This transaction hash is computed externally and is not verified by the card.

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` (`0x9c06`) error.

**Note**: When using this instruction, the limit for the maximum amount without 2FA verification is ignored and you have to always do the 2FA verification.

**INS**: `0x7a`

**P1**: key number or `0xff` for the last derived Bip32 extended key

**P2**: `0x00`

**CDATA**:

| name                                    | description                                                  | length (bytes) | default value |
| --------------------------------------- | ------------------------------------------------------------ | -------------- | ------------- |
| `tx_hash`                               | pre-computed hash of a transaction to be signed as a byte array | 32             | NA            |
| `2FA-flag` [required of 2FA is enabled] | If 2FA is enabled, this flag has to have this flag set `0x8000` or the card will return an error `SW_INCORRECT_ALG` (`0x9c09`) | 2              | `0x0000`      |
| `hmac` [required of 2FA is enabled]     | 20 bytes HMAC key for 2FA.                                   | 20             | NA            |

#### Request Examples

This example is a request sign a transaction with the latest derived BIP32 key. The transaction has was computed externally and it is: `a637ad18fabee7ad3ccd51e317091a6e16991311c0c9b83233b140b66b114448`

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x7a, 0x00, 0x00, 0x20,
   0xa6, 0x37, 0xad, 0x18, 0xfa, 0xbe, 0xe7, //
   0xad, 0x3c, 0xcd, 0x51, 0xe3, 0x17, 0x09, //
   0x1a, 0x6e, 0x16, 0x99, 0x13, 0x11, 0xc0, //
   0xc9, 0xb8, 0x32, 0x33, 0xb1, 0x40, 0xb6, // 
   0x6b, 0x11, 0x44, 0x48                    // tx_hash
}
```

#### Response

TODO

### 3.23 Instruction `set2FAKey`

#### Description

This function allows to set the 2FA key and enable 2FA. Once activated, 2FA can only be deactivated when the seed is reset.

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` (`0x9c06`) error.

**Note**: If 2FA is already enabled on the card, you will get an error `SW_2FA_INITIALIZED_KEY` (`0x9C18`)

**INS**: `0x79`

**P1**: `0x00`

**P2**: `0x00`

**CDATA**:

| name           | description                                                  | length (bytes) | default value        |
| -------------- | ------------------------------------------------------------ | -------------- | -------------------- |
| `hmacsha1_key` | 20 bytes HMAC key for 2FA.                                   | 32             | NA                   |
| `amount_limit` | Max amount (in satoshis) allowed without confirmation (this includes change value) when 2FA is enabled. This value is is a `uint64` encoded in 8 bytes in `BIG ENDIAN` or `LITTLE ENDIAN`?<br /><br />**Note**: It seems there is a bug in the implementation for checking the amount. The amount in the transaction is encoded as `LITTLE ENDIAN` but he `BigInteger.lessThan` function used uses `BIG_ENDIAN` which makes this feature impossible to use. | 2              | `0x0000000000000000` |

#### Request Examples

This example send a request to set 2FA and set the amount limit to 0 where all transactions will require 2FA authentication.

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x79, 0x00, 0x00, 0x14,
   0xa6, 0x37, 0xad, 0x18, 0xfa, 0xbe, 0xe7, //
   0xad, 0x3c, 0xcd, 0x51, 0xe3, 0x17, 0x09, //
   0x1a, 0x6e, 0x16, 0x99, 0x13, 0x11,       // hmacsha1_key
   0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, //
   0x00                                      // amount_limit
}
```

#### Response

TODO

### 3.24 Instruction `reset2FAKey`

#### Description

This function allows to reset the 2FA key and disable 2FA. Once activated, 2FA can only be deactivated when the seed is reset and all eckeys cleared.

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` (`0x9c06`) error.

**Note**: If 2FA is not already enabled on the card, you will get an error `SW_2FA_UNINITIALIZED_KEY` (`0x9C19`)

**INS**: `0x78`

**P1**: `0x00`

**P2**: `0x00`

**CDATA**:

| name           | description                | length (bytes) | default value |
| -------------- | -------------------------- | -------------- | ------------- |
| `hmacsha1_key` | 20 bytes HMAC key for 2FA. | 32             | NA            |

#### Request Examples

This example send a request to set 2FA and set the amount limit to 0 where all transactions will require 2FA authentication.

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x78, 0x00, 0x00, 0x14,
   0xa6, 0x37, 0xad, 0x18, 0xfa, 0xbe, 0xe7, //
   0xad, 0x3c, 0xcd, 0x51, 0xe3, 0x17, 0x09, //
   0x1a, 0x6e, 0x16, 0x99, 0x13, 0x11        // hmacsha1_key
}
```

#### Response

TODO

### 3.25 Instruction `cryptTransaction2FA` [Not Required]

### 3.26 Instruction `importTrustedPubkey` [Not Required]

### 3.27 Instruction `exportTrustedPubkey` [Not Required]

### 3.28 Instruction `importBIP32EncryptedSeed` [DEPRECATED]

### 3.29 Instruction `importEncryptedSecret` [Not Required]

### 3.30 Instruction `initiateSecureChannel` [Not Required]

### 3.31 Instruction `processSecureChannel` [Not Required]

### 3.32 Instruction `exportPKIPubkey`

#### Description

This function export the ECDSA secp256k1 public key that corresponds to the private key for authentikey.

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` (`0x9c06`) error.

**INS**: `0x98`

**P1**: `0x00`

**P2**: `0x00`

**CDATA**: None

#### Request Examples

This example sends a request to get the public key of authentikey.

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x98, 0x00, 0x00, 0x00
}
```

#### Response

TODO
