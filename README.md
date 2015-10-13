#CryptoSwift

Crypto related functions and helpers for [Swift](https://developer.apple.com/swift/) implemented in Swift. ([#PureSwift](https://twitter.com/hashtag/pureswift))

-------
<p align="center">
    <a href="#features">Features</a> &bull;
    <a href="#contribution">Contribution</a> &bull;
    <a href="#installation">Installation</a> &bull;
    <a href="#usage">Usage</a> &bull; 
    <a href="#author">Author</a> &bull;
    <a href="#changelog">Changelog</a>
</p>
-------

##Requirements
Good mood

##Features

- Easy to use
- Convenience extensions

##What implemented?

#### Hash
- [MD5](http://tools.ietf.org/html/rfc1321)
- [SHA1](http://tools.ietf.org/html/rfc3174)
- [SHA224](http://tools.ietf.org/html/rfc6234)
- [SHA256](http://tools.ietf.org/html/rfc6234)
- [SHA384](http://tools.ietf.org/html/rfc6234)
- [SHA512](http://tools.ietf.org/html/rfc6234)
- [CRC32](http://en.wikipedia.org/wiki/Cyclic_redundancy_check) (well, kind of hash)
- [CRC16](http://en.wikipedia.org/wiki/Cyclic_redundancy_check) (well, kind of hash)

#####Cipher
- [AES-128, AES-192, AES-256](http://csrc.nist.gov/publications/fips/fips197/fips-197.pdf)
- [ChaCha20](http://cr.yp.to/chacha/chacha-20080128.pdf)

#####Message authenticators
- [Poly1305](http://cr.yp.to/mac/poly1305-20050329.pdf)
- [HMAC](https://www.ietf.org/rfc/rfc2104.txt) MD5, SHA1, SHA256

#####Cipher block mode
- Electronic codebook ([ECB](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Electronic_codebook_.28ECB.29))
- Cipher-block chaining ([CBC](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29))
- Cipher feedback ([CFB](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher_feedback_.28CFB.29))
- Counter ([CTR](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Counter_.28CTR.29))

#####Data padding
- [PKCS#7](http://tools.ietf.org/html/rfc5652#section-6.3)

##Why
[Why?](https://github.com/krzyzanowskim/CryptoSwift/issues/5) [Because I can](https://github.com/krzyzanowskim/CryptoSwift/issues/5#issuecomment-53379391).

##Contribution

For latest version, please check **develop** branch. This is latest development version that will be merged into **master** branch at some point.

##Installation

To install CryptoSwift, add it as a submodule to your project (on the top level project directory):

	git submodule add https://github.com/krzyzanowskim/CryptoSwift.git

Then, drag the CryptoSwift.xcodeproj file into your Xcode project, and add CryptoSwift.framework as a dependency to your target. Now select your App and choose the General tab for the app target. Drag CryptoSwift.framework to "Embedded Binaries"

#####Binary distribution

Alternatively, you can build the Universal Framework and link it in your Xcode project. To do that please run `build.sh` script and find resulting frameworks in `Framework` directory.

Looking for version for Swift 1.2? check branch **swift12**, it's there.

#####iOS and OSX
By default project is setup for iOS. You need to switch to OS X SDK manually [see #8](https://github.com/krzyzanowskim/CryptoSwift/issues/8)

####CocoaPods

You can use [CocoaPods](http://cocoapods.org/?q=cryptoSwift).

```ruby
pod 'CryptoSwift'
```

or for newest version from specified branch of code:

```ruby
pod 'CryptoSwift', :git => "https://github.com/krzyzanowskim/CryptoSwift", :branch => "master"
```

####Carthage 
You can use [Carthage](https://github.com/Carthage/Carthage). 
Specify in Cartfile:

```ruby
github "krzyzanowskim/CryptoSwift"
```

Then follow [build instructions](https://github.com/Carthage/Carthage#getting-started)
 
##Usage

```swift
import CryptoSwift
```

For your convenience you should use extensions methods like encrypt(), decrypt(), md5(), sha1() and so on.

Hashing a data or array of bytes (aka `Array<UInt8>`)
```swift
/* Hash enum usage */
let input:[UInt8] = [49, 50, 51]

let output = input.md5()
// alternatively: let output = CryptoSwift.Hash.md5(input).calculate()

println(output.toHexString())
```


```swift
let data = NSData()

let hash = data.md5()
let hash = data.sha1()
let hash = data.sha224()
let hash = data.sha256()
let hash = data.sha384()
let hash = data.sha512()
	
let crc32 = data.crc32()
let crc16 = data.crc16()

println(hash.toHexString())
```
	
Hashing a String and printing result

```swift
let hash = "123".md5()
```    
    
Some content-encryption algorithms assume the input length is a multiple of k octets, where k is greater than one. For such algorithms, the input shall be padded.

```swift
let paddedData = PKCS7().add(bytes, AES.blockSize)
```

Working with Ciphers

ChaCha20

```swift
let encrypted = ChaCha20(key: key, iv: iv).encrypt(message)
let decrypted = ChaCha20(key: key, iv: iv).decrypt(encrypted)
```

AES

Notice regarding padding: *Manual padding of data is optional and CryptoSwift by default always will add PKCS7 padding before encryption, and remove after decryption when __Cipher__ enum is used. If you need manually disable/enable padding, you can do this by setting parameter for encrypt()/decrypt() on class __AES__.*

Basic:
```swift
let input = NSData()
let encrypted = try! input.encrypt(AES(key: "secret0key000000", iv:"0123456789012345"))

let input = [0,1,2,3,4,5,6,7,8,9] as [UInt8]
input.encrypt(AES(key: "secret0key000000", iv:"0123456789012345", blockMode: .CBC))
```

Advanced:
```swift
let input = [0,1,2,3,4,5,6,7,8,9] as [UInt8]

let key = [0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00] as [UInt8]
let iv = AES.randomIV(AES.blockSize)

do {
    let encrypted = try AES(key: key, iv: iv, blockMode: .CBC).encrypt(input, padding: PKCS7())
    let decrypted = try AES(key: key, iv: iv, blockMode: .CBC).decrypt(input, padding: PKCS7())
} catch AES.Error.BlockSizeExceeded {
    // block size exceeded
} catch {
    // some error
}	
```
	
AES without data padding

```swift
let input = [0,1,2,3,4,5,6,7,8,9] as [UInt8]
let encrypted = try! AES(key: "secret0key000000", iv:"0123456789012345", blockMode: .CBC).encrypt(input)
```

Using extensions
	
```swift
let plain = NSData()
let encrypted = try! plain.encrypt(ChaCha20(key: key, iv: iv))
let decrypted = try! encrypted.decrypt(ChaCha20(key: key, iv: iv))
// plain == decrypted
```
	
Message authenticators

```swift
// Calculate Message Authentication Code (MAC) for message
let mac = try! Authenticator.Poly1305(key: key).authenticate(input)
```

#####Conversion between NSData and [UInt8]

For you convenience CryptoSwift provide two function to easily convert array of bytes to NSData and other way around:

```swift
let data  = NSData.withBytes([0x01,0x02,0x03])
let bytes:[UInt8] = data.arrayOfBytes()
```

##Author
[Marcin Krzyżanowski](http://www.krzyzanowskim.com)

twitter: [@krzyzanowskim](http://twitter.com/krzyzanowskim)

##License

Copyright (C) 2014 Marcin Krzyżanowski <marcin.krzyzanowski@gmail.com>
This software is provided 'as-is', without any express or implied warranty. 

In no event will the authors be held liable for any damages arising from the use of this software. 

Permission is granted to anyone to use this software for any purpose, including commercial applications, and to alter it and redistribute it freely, subject to the following restrictions:

- The origin of this software must not be misrepresented; you must not claim that you wrote the original software. If you use this software in a product, an acknowledgment in the product documentation is required.
- Altered source versions must be plainly marked as such, and must not be misrepresented as being the original software.
- This notice may not be removed or altered from any source or binary distribution.

##Changelog

see CHANGELOG file
