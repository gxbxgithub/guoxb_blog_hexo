---
title: iOS序列化与反序列化
categories: iOS
tags:
- iOS
- 序列化
---

在iOS中一个自定义对象是无法直接存入到文件中的，必须先转化成二进制流才行。
从对象到二进制数据的过程我们一般称为对象的序列化(Serialization)，也称为归档(Archive)。
同理，从二进制数据到对象的过程一般称为反序列化或者反归档。

<!-- more -->

### NSCoder

* 归档（序列化） & 解归档（反序列化）
* 提供简单函数，在 Object 和 二进制数据间进行转换
* 抽象类 具体功能需要子类实现

#### 1. 对象实现 NSSecureCoding 协议

要实现对象的序列化，首先对象应该遵循 NSCoding 协议。因为 NSCoding 安全性不高，所以iOS6的时候，苹果引入了 NSSecureCoding 协议，加强安全性。

``` Objective-C
// ArticleModel.h

@interface ArticleModel : NSObject<NSSecureCoding>

@property (nonatomic, copy) NSString *artImg;
@property (nonatomic, copy) NSString *artTitle;
@property (nonatomic, copy) NSString *artPublishDate;
@property (nonatomic, copy) NSString *artRefer;

@end
```

```Objective-C
// ArticleModel.m

@implementation ArticleModel

// 解码方法
- (nullable instancetype)initWithCoder:(NSCoder *)aDecoder {
    self = [super init];
    if (self) {
        self.artImg = [aDecoder decodeObjectForKey:@"art_img"];
        self.artTitle = [aDecoder decodeObjectForKey:@"art_title"];
        self.artPublishDate = [aDecoder decodeObjectForKey:@"friendly_date"];
        self.artRefer = [aDecoder decodeObjectForKey:@"art_refer"];
    }
    return self;
}

// 编码方法
- (void)encodeWithCoder:(NSCoder *)aCoder {
    [aCoder encodeObject:self.artImg forKey:@"art_img"];
    [aCoder encodeObject:self.artTitle forKey:@"art_title"];
    [aCoder encodeObject:self.artPublishDate forKey:@"friendly_date"];
    [aCoder encodeObject:self.artRefer forKey:@"art_refer"];
}

// 支持加密编码
+ (BOOL)supportsSecureCoding {
    return YES;
}

@end
```

#### 2. 序列化存储 & 反序列化读取

```
NSString *cacheDir = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) firstObject];    
NSFileManager *fileManager = [NSFileManager defaultManager];
NSString *fileDir = [cacheDir stringByAppendingPathComponent:@"GTData"];
[fileManager createDirectoryAtPath:fileDir withIntermediateDirectories:YES attributes:nil error:nil];
NSString *filePath = [fileDir stringByAppendingPathComponent:@"art_list"];

// 序列化存储
NSData *artListData = [NSKeyedArchiver archivedDataWithRootObject:array requiringSecureCoding:YES error:nil];

[fileManager createFileAtPath:filePath contents:artListData attributes:nil];

// 反序列化读取
NSData *readListData = [fileManager contentsAtPath:filePath];
id unarchiveObj = [NSKeyedUnarchiver unarchivedObjectOfClasses:[NSSet setWithObjects:[NSArray class], [ArticleModel class], nil] fromData:readListData error:nil];
```