---
title: iOS9开放的新API--Spotlight使用指南
categories: iOS
tags:
- Spotlight
---
作为苹果iOS9的重要特性之一，Spotlight搜索如今重新回到主界面最左侧（同样支持主界面下滑呼出），通过API的支持，还带来了全新的Universal Search通用搜索功能，除了网络以及系统本身内容之外，还能直接搜索第三方应用内的相关内容。下面我们就来详细研究下Spotlight
<!-- more -->
### 1.Spotloight是什么?
　　Spotlight在iOS9上做了一些新的改进, 也就是开放了一些新的API, 通过Core Spotlight Framework你可以在你的app中集成Spotlight。集成Spotlight的App可以在Spotlight中搜索App的内容，并且通过内容打开相关页面。
### 2.如何集成Spotlight
###### a.添加所需要的框架
```
#if __IPHONE_OS_VERSION_MAX_ALLOWED >= 90000
#import <CoreSpotlight/CoreSpotlight.h>
#import <MobileCoreServices/MobileCoreServices.h>
#endif
```
注,很多APP都是支持iOS9以下的,因此加入#if __IPHONE_OS_VERSION_MAX_ALLOWED >= 90000,可以解决iOS9以下设备运行崩溃的问题

###### b.创建CSSearchableItemAttributeSet 对象
```
CSSearchableItemAttributeSet *attributeSet = [[CSSearchableItemAttributeSet alloc] initWithItemContentType:(NSString *)kUTTypeImage];
    attributeSet.title = spotlightTitle;// 标题
    attributeSet.keywords = keywords;// 关键字,NSArray格式
    attributeSet.contentDescription = spotlightDesc;// 描述
    attributeSet.thumbnailData = photo;// 图标, NSData格式
　　// 把图片转换成NSData的方法
　　UIImagePNGRepresentation([UIImage imageNamed:@"xxx.png"]
```

###### c.创建可检索条目CSSearchableItem
```
// spotlightInfo 可以作为一些数据传递给接受的地方
// domainId      id,通过这个id来判断是哪个spotlight
CSSearchableItem *item = [[CSSearchableItem alloc] initWithUniqueIdentifier:spotlightInfo domainIdentifier:domainId attributeSet:attributeSet];
```

###### d.添加检索入口
```
[[CSSearchableIndex defaultSearchableIndex] indexSearchableItems:@[item] completionHandler:^(NSError * error) {
     if (error) {
        NSLog(@"indexSearchableItems Error:%@",error.localizedDescription);
     } 
}];
```

###### ========完整代码========
```
-(void)insertSearchableItem:(NSData *)photo spotlightTitle:(NSString *)spotlightTitle description:(NSString *)spotlightDesc keywords:(NSArray *)keywords spotlightInfo:(NSString *)spotlightInfo domainId:(NSString *)domainId {
    CSSearchableItemAttributeSet *attributeSet = [[CSSearchableItemAttributeSet alloc] initWithItemContentType:(NSString *)kUTTypeImage];
    
    attributeSet.title = spotlightTitle;                // 标题
    attributeSet.keywords = keywords;                   // 关键字,NSArray格式
    attributeSet.contentDescription = spotlightDesc;    // 描述
    attributeSet.thumbnailData = photo;                 // 图标, NSData格式
    
    // spotlightInfo 可以作为一些数据传递给接受的地方
    // domainId      id,通过这个id来判断是哪个spotlight
    CSSearchableItem *item = [[CSSearchableItem alloc] initWithUniqueIdentifier:spotlightInfo domainIdentifier:domainId attributeSet:attributeSet];
    
    [[CSSearchableIndex defaultSearchableIndex] indexSearchableItems:@[item] completionHandler:^(NSError * error) {
        if (error) {
            NSLog(@"indexSearchableItems Error:%@",error.localizedDescription);
           
        }
    }];
}
```

###### ========加载本地图片的使用方法========
```
[self insertSearchableItem:UIImagePNGRepresentation([UIImage imageNamed:@"xxx.png"]) spotlightTitle:@"等风来" description:@"等风来描述" keywords:@[@"鲍鲸鲸",@"大丽花"] spotlightInfo:@"传递过去的值" domainId:@"com.wb.spotlight"];
```

###### ========加载网络图片的使用方法========
```
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        NSData * data = [NSData dataWithContentsOfURL:[NSURL URLWithString:@"http://hiphotos.baidu.com/doc/pic/item/eaf81a4c510fd9f905f61934262dd42a2934a48e.jpg"]];
        [self insertSearchableItem:data spotlightTitle:@"等风来" description:@"等风来描述" keywords:@[@"鲍鲸鲸",@"大丽花"] spotlightInfo:@"传递过去的值" domainId:@"com.wb.spotlight"];
    });
```

###### ========删除所有spotlight的方法========
```
[[CSSearchableIndex defaultSearchableIndex] deleteAllSearchableItemsWithCompletionHandler:^(NSError * _Nullable error) {
if (error) {
NSLog(@"%@", error.localizedDescription);
}
}];
```

###### ========删除指定的spotlight的方法========
```
[[CSSearchableIndex defaultSearchableIndex] deleteSearchableItemsWithDomainIdentifiers:@"domainId" completionHandler:^(NSError * _Nullable error) {
if (error) {
NSLog(@"%@", error.localizedDescription);
}
}];
```

###### ========点击spotlight后的响应方法========
```
- (BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity restorationHandler:(void (^)(NSArray * _Nullable))restorationHandler {
if ([[userActivity activityType] isEqualToString:CSSearchableItemActionType]) {
NSString *uniqueIdentifier = [userActivity.userInfo objectForKey:CSSearchableItemActivityIdentifier];
// 接受事先定义好的数值,如果是多个参数可以使用把json转成string传递过来,接受后把string在转换为json
NSLog(@"传递过来的值%@", uniqueIdentifier);
}
return YES;
}
```

###### 备注:
```
#if __IPHONE_OS_VERSION_MAX_ALLOWED >= 90000
　　// 相关spotlight的方法等
#endif

// Spotlight支持iOS9以上设备运行,对与低版本的设备需加入这个防止崩溃问题
```