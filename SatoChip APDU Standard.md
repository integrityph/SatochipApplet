# SatoChip APDU Standard

This document provides a complete list of APDU instructions used with SatoChip JavaCard applet.

## 1. Table of Contests

[TOC]

## 2. APDU Specifications

In APDU communication, a packet is sent to a Java Card applet which processes the request and sends back a response. This is the communication protocol used to communicate with the card using the ISO7816 chip connection or NFC.

### 2.1 Request Format

The request consists of a header and a body. The request is formatted at follows:

| CLA    | INS    | P1     | P2     | LC                | CDATA          | LE                |
| ------ | ------ | ------ | ------ | ----------------- | -------------- | ----------------- |
| 1 byte | 1 byte | 1 byte | 1 byte | 1 byte [optional] | var [optional] | 1 byte [optional] |

**CLA**: The class of which is used to distinguish different products and products classes. For SatoChip, this value is always `0xb0`.

**INS**: The instruction. For a complete list of instructions, check [3 APDU Instructions].

**P1, P2**: parameter 1 and parameter 2 are two values to be passed to the Java Card with the request. For possible values, check the instruction documentation below.

**LC**: The length of `CDATA`. Can be omitted if the value is `0x00`.

**CDATA**: is an array of bytes of length of 1 to 255.

**LE**: explicit length of response. This can be omitted or sat to value `0x00` if there is no expected response of when the length of the response is unknown.

### 2.2 Response Format



## 3. APDU Instructions



### 3.1 Instruction `setup`

#### Description

This instruction is supposed to be used one during the initialization of the card.

**INS**: `0xa1`

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
| `amount_limit` [optional] | Max amount (in satoshis) allowed without confirmation (this includes change value) when 2FA is enabled. This value is is a `uint64` encoded in 8 bytes in `BIG ENDIAN` or `LITTLE ENDIAN`? | 8              | NA                                                  |

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

#### Response

TODO

### 3.2 Instruction `importKey`

#### Description

This function allows the import of a private ECkey into the card.

**INS**: `0x32`

**P1**: private key number `0x00` to `0x0f` (NOTE: if there is already a key at the specified number the card will return `SW_INCORRECT_P1`)

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

#### Response

TODO

### 3.3 Instruction `resetKey`

#### Description

This function allows to reset a private ECkey stored in the card. If 2FA is enabled, a hmac code must be provided to reset the key.

**INS**: `0x33`

**P1**: private key number `0x00` to `0x0f` (NOTE: if there is no key at the specified number the card will return `SW_INCORRECT_P1`)

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

#### Response

TODO

### 3.4 Instruction `getPublicKeyFromPrivate`

#### Description

This function returns the public key associated with a particular private key stored in the applet. The exact key blob contents depend on the key algorithm and type.

**INS**: `0x35`

**P1**: private key number `0x00` to `0x0f` (NOTE: if there is no key at the specified number the card will return `SW_INCORRECT_P1`)

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

**P1**: PIN number `0x00`-`0x07` (NOTE: if there is a PIN already at the specified number the card will return `SW_INCORRECT_P1`)

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

**P1**: PIN number `0x00`-`0x07` (NOTE: if there is no PIN at the specified number the card will return `SW_INCORRECT_P1`)

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

#### Response

TODO

### 3.7 Instruction `changePIN`

#### Description

This function changes a PIN code. The DATA portion contains both the old and the new PIN codes.

**INS**: `0x44`

**P1**: PIN number `0x00`-`0x07` (NOTE: if there is no PIN at the specified number the card will return `SW_INCORRECT_P1`)

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

#### Response

TODO

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

#### Response

TODO

### 3.9 Instruction `listPINs`

#### Description

This function returns a 2 byte bit mask of the available PINs that are currently in use. Each set bit corresponds to an active PIN.

**Note**:  PIN 0 has to be verified before this instruction in requested or the card will return `SW_UNAUTHORIZED` error.

**INS**: `0x48`

**P1**: `0x00`

**P2**: `0x00`

**CDATA**: None

#### Request Examples

This example requests a list of all available PINs

```c++
// CLA   INS   P1    P2    LC    CDATA ...
{  0xb0, 0x48, 0x00, 0x00, 0x00,
}
```

#### Response

TODO

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

**INS**: `0x3C`

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

#### Response

TODO
