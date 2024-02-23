---
title: 为你的APP更换皮肤、主题
categories: iOS
tags: 
- iOS-App皮肤
---
最近在项目里面有个模块涉及到主题设置，在网上学习了很多篇文章，这篇文章主要谈一下个人的做法。

大致的步骤如下：

* 整个应用依赖于一个主题管理器，主题管理器根据当前的主题配置，加载不同主题文件夹下的主题
* 在需要更换主题的地方创建统一通知【我的做法是：例如更换所有UILabel的字体颜色，创建UILabel基类，所有涉及主题切换的label都继承UILabel基类，在基类中创建通知】
* 切换主题，发送统一通知更换主题
<!--more-->
### 1.主题管理器
![theme_manager](http://img.haoyunbang.cn/app/2016-08-23/3849E26F-78C0-4B9A-BEFF-5C0666D6C1DB/509qdl0t.jpg@280w)

这是主题管理器文件夹下所有涉及主题切换的基类，ThemeManager为管理器

ThemeManager被构建为是单例，在实例初始化的时候，同时实例化主题的相关配置：

```
+ (ThemeManager*)sharedInstance{
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        themeManager = [[self alloc] init];
    });
    return themeManager;
}

/**
 ** 初始化
 **/
-(instancetype)init{
    if (self = [super init]) {
    	//设置默认主题
        [self makeDefaultTheme];
    }
    return self;
}
```
### 2.主题设置的主要代码

```
/**
 ** 设置主题
 **/
-(void)makeDefaultTheme{
    /**
     ** 创建Theme文件夹在Cache文件夹下，用于存放主题文件夹
     **/
    NSString *themePath = [[NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) objectAtIndex:0] stringByAppendingPathComponent:@"Theme"];
    //文件管理器
    NSFileManager *fileManager = [NSFileManager defaultManager];
    if (![fileManager fileExistsAtPath:themePath]) {
        [fileManager createDirectoryAtPath:themePath withIntermediateDirectories:YES attributes:nil error:nil];
    }
    NSLog(@"themePath = %@",themePath);
    //主题基本路径
    self.themeBasePath = themePath;
    /**
     ** 设置主题
     **/
    NSString *themeName = [[Config Instance] getThemeName];
//    themeName = @"ThemeDefault";
    NSString *plistPath = @"";
    if ([themeName isEqualToString:@"ThemeDefault"]) {
        plistPath = [[NSBundle mainBundle] pathForResource:@"ThemeDefault" ofType:@"plist"];
    }else{
        plistPath = [[themePath stringByAppendingPathComponent:[NSString stringWithFormat:@"skin_%@",[[Config Instance] getThemeId]]] stringByAppendingPathComponent:@"theme.plist"];
    }
    NSDictionary *dictionary = [[NSDictionary alloc] initWithContentsOfFile:plistPath];
    NSUserDefaults *userDefaults = [NSUserDefaults standardUserDefaults];
    [userDefaults setObject:dictionary forKey:@"kAllColorDict"];
    [userDefaults synchronize];

    /**
     ** 初始化本类属性
     **/
    //主题名称
    self.themeName = themeName;
    //主题路径
    self.themePath = [themePath stringByAppendingPathComponent:[NSString stringWithFormat:@"skin_%@",[[Config Instance] getThemeId]]];
}
```
执行这段代码，会有一个关于主题配置的.plish文件存在本地，之后发送通知设置主题即可。。。

### 3.附上基类代码
`ThemeLabel.m文件`

```
//1.alloc init之后执行该方法，注册通知设置默认主题皮肤
-(void)makeThemeLabelColor:(ThemeLabelColor *)color{
    self.labelColor = color;
    [self loadLabelColor];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(themeNotification:) name:kThemeDidChangeNotification object:nil];
}

//2.更换主题会执行该方法，改变主题
-(void)themeNotification:(NSNotification *)notification{
    [self loadLabelColor];
}

//3.更换主题操作
-(void)loadLabelColor{
    //size
    if (self.labelColor == ThemeLabelSize) {
        self.frame = CGRectMake(0, 0, SCREENWIDTH, 0);
        return;
    }
    //header
    if (self.labelColor == ThemeLabelHeaderBgColor) {
        self.backgroundColor = kMyThemeColor(kBgColor);
        return;
    }
    //color
    UIColor *textColor = [UIColor whiteColor];
    if (self.labelColor == ThemeLabelTopTitleColor) {
        textColor = kMyThemeColor(kTopTitileColor);
    }else if (self.labelColor == ThemeLabelCreditErrorColor){
        textColor = kMyThemeColor(kCreditErrorColor);
    }
    self.textColor = textColor;
}
```

【ps：以上是我在项目中更换主题皮肤的简单操作，需求比较简单，更换颜色就行】