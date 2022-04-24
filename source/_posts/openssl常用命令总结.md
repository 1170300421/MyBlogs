---
title: openssl常用命令总结
date: 2022-02-20 14:09:02
tags:
    - openssl
---

## 前提

本次是对平时使用较多的一些openssl命令进行总结，方便后续进行查找

<!--more-->

## 正文

### 1.生成证书请求文件

```
openssl ecparam -genkey -name prime256v1 -out server.key
openssl req -subj "/C=CN/ST= /L= /O= /OU= /CN= /emailAddress=  -new -key server.key -out server.csr -sha256
```

根据实际填入对应名字

### 2.验证证书签名

```
openssl verify -CAfile 根证书 测试证书
```

### 3.解析证书文件

```
openssl x509 -in 测试证书 -noout -text
openssl x509 -in certificate.der -inform der -text -noout //解析der格式证书
```

### 4.pkcs证书格式转化

```
openssl pkcs8 -topk8 -inform PEM -in private_pkcs1.pem -outform pem -nocrypt -out private_pkcs8.pem //pkcs1转pkcs8
openssl pkcs12 -export -out ssl2_me.pfx -inkey ssl2_me.key -in ssl2_me.crt //PKCS12转cer
```

### 5.生成公私钥

```
//生成私钥
openssl ecparam -name secp256k1 -genkey -noout -out ecprikey.pem //ECC
openssl genrsa -out rsa_private.key 2048 //RSA

//生成 Pem 公钥
openssl ec -in ecprikey.pem -pubout -out ecpubkey.pem //ECC
openssl rsa -in rsa_private.key -pubout -out rsa_public.key //rsa

//生成 二进制形式 公钥
openssl ec -outform der -in ecprikey.pem -pubout -out ecpubkey.der
```

### 6.提取证书公钥

```
openssl x509 -in XX.cer -pubkey  -noout > XX.pem
```

### 7.签名验签

```
openssl dgst -sha256 -verify 公钥文件 -signature 签名数据(字节形式)文件 签名数据文件  //验签
openssl dgst -sign 私钥文件 -sha256 -out 签名数据(字节形式)文件 签名数据文件 //签名
```
