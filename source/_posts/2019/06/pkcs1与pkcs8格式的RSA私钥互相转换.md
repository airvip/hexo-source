---
title: pkcs1与pkcs8格式的RSA私钥互相转换
date: 2019-06-16 17:24:51
tags: 
    - Openssl
    - RSA
---

> 古往传奇多少别离，我只是不经意遇到了你!

RSA 私钥和公钥文件常见的格式有 (pkcs#7, pkcs#8, pkcs#12, pem), pem 这种格式也叫 pkcs#1,在 PHP 中我们通常使用 pkcs#1 (pem) 这种格式，而在 Java 中就经常使用 pkcs#8 这种格式，为了方便不同语言做签名，就需要对我们的秘钥做不同格式的相互转化。

<!-- more  -->

# pkcs#1 及 pkcs#8 公私钥的格式

## 公钥格式

1、 RSA Public Key file (PKCS#1) 即：PKCS#1 格式的公钥文件格式

```
-----BEGIN RSA PUBLIC KEY-----
BASE64 ENCODED DATA
-----END RSA PUBLIC KEY-----
```

在 BASE64 ENCODED DATA 中存在以下 DER 结构

```
RSAPublicKey ::= SEQUENCE {
    modulus           INTEGER,  -- n
    publicExponent    INTEGER   -- e
}
```

因为 RSA 不仅仅在 x509 和 ssl/tls 中使用的，所以可以使用更通用的密钥格式，即 pkcs 8，它标识公钥的类型并包含相关数据。

2、 Public Key file (PKCS#8) 即：PKCS#8 格式的公钥文件格式

```
-----BEGIN PUBLIC KEY-----
BASE64 ENCODED DATA
-----END PUBLIC KEY-----
```

在 BASE64 ENCODED DATA 中存在以下 DER 结构

```
PublicKeyInfo ::= SEQUENCE {
  algorithm       AlgorithmIdentifier,
  PublicKey       BIT STRING
}
 
AlgorithmIdentifier ::= SEQUENCE {
  algorithm       OBJECT IDENTIFIER,
  parameters      ANY DEFINED BY algorithm OPTIONAL
}
```

## 私钥格式

1、RSA Private Key file (PKCS#1) 即：PKCS#1 格式的私钥文件格式

```
-----BEGIN RSA PRIVATE KEY-----
BASE64 ENCODED DATA
-----END RSA PRIVATE KEY-----
```

在 BASE64 ENCODED DATA 中存在以下 DER 结构

```
RSAPrivateKey ::= SEQUENCE {
  version           Version,
  modulus           INTEGER,  -- n
  publicExponent    INTEGER,  -- e
  privateExponent   INTEGER,  -- d
  prime1            INTEGER,  -- p
  prime2            INTEGER,  -- q
  exponent1         INTEGER,  -- d mod (p-1)
  exponent2         INTEGER,  -- d mod (q-1)
  coefficient       INTEGER,  -- (inverse of q) mod p
  otherPrimeInfos   OtherPrimeInfos OPTIONAL
}
```

2、Private Key file (PKCS#8) 即：PKCS#8 格式的私钥文件格式

```
-----BEGIN PRIVATE KEY-----
BASE64 ENCODED DATA
-----END PRIVATE KEY-----
```

在 BASE64 ENCODED DATA 中存在以下 DER 结构

```
PrivateKeyInfo ::= SEQUENCE {
  version         Version,
  algorithm       AlgorithmIdentifier,
  PrivateKey      BIT STRING
}
 
AlgorithmIdentifier ::= SEQUENCE {
  algorithm       OBJECT IDENTIFIER,
  parameters      ANY DEFINED BY algorithm OPTIONAL
}
```

加密过 PKCS#8 格式的私钥文件格式

```
-----BEGIN ENCRYPTED PRIVATE KEY-----
BASE64 ENCODED DATA
-----END ENCRYPTED PRIVATE KEY-----
```

在 BASE64 ENCODED DATA 中存在以下 DER 结构

```
EncryptedPrivateKeyInfo ::= SEQUENCE {
  encryptionAlgorithm  EncryptionAlgorithmIdentifier,
  encryptedData        EncryptedData
}
 
EncryptionAlgorithmIdentifier ::= AlgorithmIdentifier
 
EncryptedData ::= OCTET STRING
```

EncryptedData ::= OCTET STRING 的 OCTET STRING 就是 PKCS#8 的 PrivateKeyInfo。

# pkcs#1 及 pkcs#8 私钥的转换

1、pkcs#1 私钥生成

```
airvip@airvip:~$ openssl genrsa -out private.pem 1024
```

private.pem 文件内容如下

```
-----BEGIN RSA PRIVATE KEY-----
MIICXgIBAAKBgQDV2MOvqN8ukif9GIKY+jjjzQx/a4KfaCx3xcqcS0C21qGmhtDM
futUXEZ6o9SPJNboKVzyXWaXZ+BKXQtUE9HyIYIC9YcTPAIH3HEvDvv1p6TshPD1
Ne9VGXC4odbu8mmMzPB4ZWGkocz2EhEFYI7k0YjdmPI4eejsy2902gNzdQIDAQAB
AoGBAML+QBl5w+BkV2BarCMv7xYRUDcT4mzWgc3JJmHz4JOYDJibT+NYS9+FsKJ9
pwHLlsbliKJbdoPSrVtBkmXdGM6jvTkj1dBUDqDHcH2oXTf9d8s5hmLZ8AnTKiL0
1zQ2pBH48/5extydweE9SKnfGPRdIN7a4PU2PHAMnzbcHmc1AkEA/Hiq4s/efm8V
tLP007QfFTZJc9Xp1n95va0fMOcU8l8debl+eLtRLnSkG5/k9cIfk0piCjKpqzcz
iIpzl4V4kwJBANjV5kaql/W6Kl0X+gRU93toQ452RsagmqU4RSkLVRbgevJ1loW7
od09f7SFpmaQWmCVJT4vwaJ9ja5jf0BWENcCQQDrgmx1804rRthKnmvNaAJuuRs/
sNXWv0cDBpFH/udtSAio2b3+RU3vNeM32amtDo6BySs3NBdNncq2neBCAcF9AkAZ
5lK/FhlYjIgK3pZKdiupXebT2WTSzNQr7/Cy2lurg9yroUf9aVAIMRRT57LchVmG
N8pfBy1Fz2Fg9xl4Ac+5AkEA3++8TZs9OAwPnytd8mL/obiBRBr8d8ncFQjxcSq+
EFMHkUp/5Z8napVuFgC/sbTLJ510soZttflTaWCzYZrDMA==
-----END RSA PRIVATE KEY-----
```

2、pkcs#1 格式私钥转换为 pkcs#8 格式私钥

```
airvip@airvip:~$ openssl pkcs8 -topk8 -inform PEM -in private.pem -outform pem -nocrypt -out pkcs8.pem
```

pkcs8.pem 文件内容如下

```
-----BEGIN PRIVATE KEY-----
MIICeAIBADANBgkqhkiG9w0BAQEFAASCAmIwggJeAgEAAoGBANXYw6+o3y6SJ/0Y
gpj6OOPNDH9rgp9oLHfFypxLQLbWoaaG0Mx+61RcRnqj1I8k1ugpXPJdZpdn4Epd
C1QT0fIhggL1hxM8AgfccS8O+/WnpOyE8PU171UZcLih1u7yaYzM8HhlYaShzPYS
EQVgjuTRiN2Y8jh56OzLb3TaA3N1AgMBAAECgYEAwv5AGXnD4GRXYFqsIy/vFhFQ
NxPibNaBzckmYfPgk5gMmJtP41hL34Wwon2nAcuWxuWIolt2g9KtW0GSZd0YzqO9
OSPV0FQOoMdwfahdN/13yzmGYtnwCdMqIvTXNDakEfjz/l7G3J3B4T1Iqd8Y9F0g
3trg9TY8cAyfNtweZzUCQQD8eKriz95+bxW0s/TTtB8VNklz1enWf3m9rR8w5xTy
Xx15uX54u1EudKQbn+T1wh+TSmIKMqmrNzOIinOXhXiTAkEA2NXmRqqX9boqXRf6
BFT3e2hDjnZGxqCapThFKQtVFuB68nWWhbuh3T1/tIWmZpBaYJUlPi/Bon2NrmN/
QFYQ1wJBAOuCbHXzTitG2Eqea81oAm65Gz+w1da/RwMGkUf+521ICKjZvf5FTe81
4zfZqa0OjoHJKzc0F02dyrad4EIBwX0CQBnmUr8WGViMiArelkp2K6ld5tPZZNLM
1Cvv8LLaW6uD3KuhR/1pUAgxFFPnstyFWYY3yl8HLUXPYWD3GXgBz7kCQQDf77xN
mz04DA+fK13yYv+huIFEGvx3ydwVCPFxKr4QUweRSn/lnydqlW4WAL+xtMsnnXSy
hm21+VNpYLNhmsMw
-----END PRIVATE KEY-----
```

3、pkcs#8 格式私钥转换为 pkcs#1 格式私钥

```
airvip@airvip:~$ openssl rsa -in pkcs8.pem -out pkcs1.pem
```

pkcs1.pem 文件内容 与 private.pem 文件内容一样。
