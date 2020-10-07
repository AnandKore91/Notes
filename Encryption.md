# Encryption 

## Encryption types, why?

## Three type: 
1. Symmetric 
2. Asymmetric
3. Hash encryption. 

The Advanced Encryption Standard (AES) is a symmetric block cipher

## Libs / 3rd Party
* https://github.com/krzyzanowskim/CryptoSwift
* https://medium.com/@vialyx/security-data-transforms-with-swift-aes256-on-ios-6509917497d

## Symmetric cryptography
* encrypter, and decrypter — need access to the same key.
* The tricky part is how to store the key and make it available only to the software that needs it.
* Best use: 
    1. In services that store encrypted data on behalf of a user (like cloud backup services)
    2. To encrypt computer or device storage(Computer password)
    3. To create a secure channel between two network endpoints, provided there’s a separate scheme for securely exchanging the key

## Asymmetric cryptography (public key cryptography)
* known as public key cryptography, uses public and private keys to encrypt and decrypt data.
* Either of the keys can be used to encrypt a message; the opposite key from the one used to encrypt the message is used for decryption.

## Hash
* Hashing is used only to verify data
* the same input will always produce the same output
* it’s impossible to reverse it back to the original data
* given knowledge of only the hash, it’s infeasible to create another string of data that will create the same hash (called a “collision” in crypto parlance)
* Three resistance:
    - Pre-Image Resistance: 
    - when: h(x) = z
    - hard to find: h(z)-1 = x
* Second Pre-Image Resistance: if a hash function h for an input x produces hash value h(x), then it should be difficult to find any other input value y such that h(y) = h(x).

￼![TypesOfEncryption.png](https://github.com/AnandKore91/Notes/blob/master/Images/TypesOfEncryption.png "TypesOfEncryption.png")
￼

## How Public - Private key works.?
￼
￼![AsymmetricEncryption.png](https://github.com/AnandKore91/Notes/blob/master/Images/AsymmetricEncryption.png "AsymmetricEncryption.png")
