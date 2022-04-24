---
title: iOS常用数据转化
date: 2022-02-07 12:23:50
tags:
    - iOS
    - 数据转化
---

## 前提

本次主要是对自己在平时开发过程经常遇到的一些Objective-c数据格式的转化进行总结，方便后续可以直接使用。

<!--more-->

## 正文

### 1.NSData数据转NSString数据

```
NSString *String = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
```

### 2.NSString数据转NSData数据

```
NSData *data = [String dataUsingEncoding:NSUTF8StringEncoding];
```

### 3.NSData数据转Base64数据

```
NSString *base64 = [data base64EncodedStringWithOptions:NSDataBase64Encoding64CharacterLineLength];
```

### 4.NSData数据转十六进制字符串

```
- (NSData *)HexStrToData:(NSString *)str
{
    NSMutableData *hexData = [[NSMutableData alloc] initWithCapacity:20];
    NSRange range;
    if ([str length] % 2 == 0) {
        range = NSMakeRange(0, 2);
    } else {
        range = NSMakeRange(0, 1);
    }
    for (NSInteger i = range.location; i < [str length]; i += 2) {
        unsigned int anInt;
        NSString *hexCharStr = [str substringWithRange:range];
        NSScanner *scanner = [[NSScanner alloc] initWithString:hexCharStr];
        
        [scanner scanHexInt:&anInt];
        NSData *entity = [[NSData alloc] initWithBytes:&anInt length:1];
        [hexData appendData:entity];
        
        range.location += range.length;
        range.length = 2;
    }
    return hexData;
}
```

### 5.十六进制字符串转NSData数据

```
- (NSString *)DataToHexStr:(NSData *)data
{
    if (![data isKindOfClass:[NSData class]] || data.length == 0) {
        return nil;
    }
    
    NSMutableString *hexStr = [NSMutableString string];
    const char *buf = data.bytes;
    for (int i = 0; i < data.length; i++)
    {
        [hexStr appendFormat:@"%02X", buf[i] & 0xff];
    }
    return hexStr;
}
```

### 6.普通NSData、NSString与base64data互转

```
NSData *base64data = [data base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithLineFeed];//普通data-> base64data

NSData *b64CertData= [[NSData alloc] initWithBase64EncodedString:pemStr options:NSDataBase64DecodingIgnoreUnknownCharacters];//NSString->base64Data

NSData *data = [[NSData alloc] initWithBase64EncodedData:base64data options:NSDataBase64DecodingIgnoreUnknownCharacters]; //base64data->普通data
```

### 7.NSString与UInt8数据互相转化及UInt8数据转NSData

```
//NSString 转 UInt8
NSString *str = @"测试转换";
UInt8 buff_str[1024];
memcpy(buff_str,[str UTF8String], [str length]+1);
NSLog(@"char = %s",buff_str);

//UInt8 转 NSString
NSString *str_From_buff = [NSString stringWithCString:(char*)buff_str encoding:NSUTF8StringEncoding];
NSLog(@"string = %@",str_From_buff);

//UInt8 转 NSData
tmp_str=[NSData dataWithBytes:shares[0] length:sizeof(shares[0])];

注：因为dataWithBytesNoCopy:想保留你给它的指针。事实上，你必须给它一个你创建的指针malloc，因为它会在完成数据后释放它。因此使用此可能出现pointer being freed was not allocated的内存错误 所以使用dataWithBytes来进行数据转化
```

### 8.SeckeyRef数据与NSData数据转化

```
SeckeyRef seckeyrefData = (__bridge CFDataRef)data //SeckeyRef -> NSData

NSData *data = (__bridge NSData*)seckeyrefData //NSData -> SeckeyRef
```
