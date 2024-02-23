
---
title: iOS - Runtime 总结
categories: iOS
tags:
- runtime
---
OC是一门动态语言，它会把一些编译时做的决定推迟到运行时。因此，仅仅有编译器是不够的，还需要一个运行时系统（Runtime System）来执行编译后的代码。

Runtime系统的核心是一个用C，C++以及在最核心的消息发送部分甚至使用汇编语言编写的一套底层API库，它是OC面向对象和动态发送消息的基石。

简单来说， Runtime 是一个库，这个库使我们可以在程序运行时创建对象、检查对象，修改类和对象的方法。
<!-- more -->

首先附上源码下载地址：[Runtime开源代码](https://opensource.apple.com/tarballs/objc4/)

## 一. Runtime是什么？

OC是一门动态语言，它会把一些编译时做的决定推迟到运行时。因此，仅仅有编译器是不够的，还需要一个运行时系统（Runtime System）来执行编译后的代码。

Runtime系统的核心是一个用C，C++以及在最核心的消息发送部分甚至使用汇编语言编写的一套底层API库，它是OC面向对象和动态发送消息的基石。

简单来说， Runtime 是一个库，这个库使我们可以在程序运行时创建对象、检查对象，修改类和对象的方法。

## 二. Runtime是做什么的？有什么用？

首先，通过Runtime库可以：

1. 获取类里面的所有成员变量；
2. 为类动态添加成员变量；
3. 动态改变类的方法实现；
4. 为类动态添加新的方法；

**重点**：Runtime属于OC的底层，我们可以使用Runtime进行一些非常底层的操作，而这些操作是OC无法做到的，或者是OC不容易实现的。

## 三. Runtime是怎么工作的？

先来了解几个概念

#### 1. Class 和 Object

objc.h 中，Class 被定义为指向 objc_class 的指针，定义如下：
`typedef struct objc_class *Class;`

objc_class  是一个结构体，在 runtime.h 中定义如下：

```
struct objc_class {
    Class isa;                                // 实现方法调用的关键
    Class super_class;                        // 父类
    const char * name;                        // 类名
    long version;                             // 类的版本信息，默认为0
    long info;                                // 类信息，供运行期使用的一些位标识
    long instance_size;                       // 该类的实例变量大小
    struct objc_ivar_list * ivars;            // 该类的成员变量链表
    struct objc_method_list ** methodLists;   // 方法定义的链表
    struct objc_cache * cache;                // 方法缓存
    struct objc_protocol_list * protocols;    // 协议链表
};
```

> 提示：在 Xcode 中，使用快捷键 cmd + shift + o，在搜索框中输入 objc_class 即可查看定义

通过查看这些定义，我们可以知道：一个类保存了自身所有的成员变量，所有的方法，所有的实现的协议。

还有两个比较重要的字段 isa 和 cache，isa 指向对应类的元类，下面在元类篇幅会讲到，在探索 cache 之前，先看下 OC 中对象的定义：

```
struct objc_object {
    Class isa;
};
typedef struct objc_object *id;
```

objc_object 中只包含一个 isa 指针，而 id 是一个指向 objc_object 的指针。

也就是说，一个对象唯一保存的信息就是它的 Class 地址，当调用一个对象的方法时，会通过 isa 找到对应的 objc_class，然后再在 objc_class 的方法列表中找到对应的方法执行。

再说 cache，因为调用方法实际上是从方法列表 methodLists 中查找的过程，如果每次都去 methodLists 中查找，效率会很低。所以对调用过的方法，会以 map 的方式缓存在 cache 中，下次调用会快很多。

#### 2. Meta Class 元类

观察 objc_class 和 objc_object 的定义，不难发现两者本质相同，只是包含的字段不同，在 OC 中，**类也被设计为一个对象，类的类型就是元类（Meta Class）**。

**OC中每个类都有对应的元类**，元类的 methodLists 中保存了类的方法链表，即所谓的 **类方法**。类的 isa 指针指向对应的元类（上面的问题，在此处有了答案）。

所以，调用一个对象的类方法的过程如下：

1. 通过对象的 isa 指针找到对应的类；
2. 通过类的 isa 指针找到对应的元类；
3. 在元类 methodLists 中找到对应方法，然后执行。

元类也有一个 isa 指针，元类也是一个对象。

元类的 isa 指向基类的元类，基类的元类的 isa 指向自己。

<img src="iOS Runtime总结/20191212171727713.jpg">

#### 3. Method

上面讲到，「找到对应的方法，然后执行」，那么这个「执行」是怎样进行的呢？下面就来介绍一下 OC 中的方法调用。

Method 在 runtime.h 中的定义如下：

```
typedef struct objc_method *Method;

struct objc_method {
    SEL method_name;
    char * method_types;
    IMP method_imp;
};
```

这里 Method 被定义为一个 objc_method 指针，在 objc_method 结构体中，包含一个 SEL 和 一个IMP，同样看下它们的定义：

```
// SEL
typedef struct objc_selector *SEL;

// IMP
typedef void (*IMP)(void /* id, SEL, ... */ ); 
```

##### （1）SEL 是指向 objc_selector 的指针，而 objc_selector 在头文件找不到定义，测试下：

```
SEL sel = @selector(helloworld);
NSLog(@"%s", sel);          // 输出：helloworld
SEL sel1 = @selector(viewDidLoad1);
NSLog(@"%s", sel1);         // 输出：viewDidLoad1
```

因此，我们可以认为：**SEL是一个保存方法名的字符串**。

##### （2）IMP 是一个「函数指针」，就是用来找到函数地址，然后执行函数的

**注意**：IMP 指向的函数的前两个参数是默认参数，id 和 SEL。id 对于实例方法来说，self 保存了当前对象地址；对与类方法来说，self 保存了当前对应类对象的地址。后面的省略号是参数列表。

综上：Method 建立了 SEL 和 IMP 的关联，当对一个对象发送消息时，会通过 SEL 找到 IMP，然后执行。

##### （3）在 OC 中，所有的方法调用，都会转化为向对象发送消息。发送消息使用 objc_msgSend 函数

```
// objc_msgSend
objc_msgSend(void /* id self, SEL op, ... */ )

// 上面提到的 IMP
typedef void (*IMP)(void /* id, SEL, ... */ ); 
```

可以发现，参数列表和 IMP 指向的函数参数列表是相对应的。

以下两段代码是相等的：

```
// OC 实现
[self doSomething];

// runtime 实现
objc_msgSend(self, @selector(doSomethiing));
```

##### （4）objc_msgSend 会默认传入 id 和 SEL，这对应了两个隐含参数，self 和 _cmd。意味着，我们可以在方法实现过程中拿到它们，并使用它们。看代码：

```
- (void)testCmd:(NSNumber *)num {
    NSLog(@"%ld", (long)num.integerValue);
    num = [NSNumber numberWithInteger:num.integerValue-1];
    if (num.integerValue > 0) {
        [self performSelector:_cmd withObject:num];
    }
}
```

尝试调用：

`[self testCmd:@(5)];`

输出：5，4，3，2，1

即我们可以在方法内部用 _cmd 调用方法自身。

##### （5）下面讨论类之间继承的情况，objc_class 结构体中，包含一个指向父类的指针 super_class

即当向一个对象发送消息时，会去这个类的 methodLists 中查找相应的 SEL ，如果查不到，则通过 super_class 指针找到父类，再去父类的 methodLists 中查找，层层递进。最后仍然找不到，才走抛异常流程。

<img src="iOS Runtime总结/2019121218280385.jpg">

##### （6）当一个方法找不到的时候，会走拦截调用和消息转发流程

我们可以重写 +resolveClassMethod: 和 +resolveInstanceMethod: 方法，在程序崩溃前做一些处理。通常的做法是动态添加一个方法，并返回 YES 告诉程序已经成功处理消息。如果这两个方法返回 NO ，这个流程会继续往下走，完整的流程如下图所示：

<img src="iOS Runtime总结/20191212182957183.jpg">

## 四. 用 Runtime 实现具体功能

上面介绍了 Runtime 的原理，以及一些基本概念，下面上代码体验一下 Runtime 的魅力。

项目中使用 Runtime ，请首先引入这两个头文件：

```
#import <objc/runtime.h>
#import <objc/message.h>
```

### 1. 消息转发

在 VC 中调用 `Person` 类的 `sendMessage` 方法，使用 OC 我们是这样调用的：
`[[Person new] sendMessage:@"hello"];`

用 Runtime 可以这样写：
`objc_msgSend([Person new], @selector(sendMessage:), @"world");`

这里不再过多解释，上面有提到~

这里引入另一个问题，如果 `Person` 中没有 `sendMessage` 方法，程序肯定会崩溃，如果防止崩溃呢？那就用到了 Runtime 消息转发流程。

上面有 消息转发流程图 ，我们分三步走，这里我们直接上代码：

（1）`Person.m` 重写 `+ (BOOL)resolveInstanceMethod` 方法

```
void sendMsg(id self, SEL _cmd, NSString *msg) {
    NSLog(@"%@", msg);
}

// 1. resolveInstanceMethod 动态方法解析
+ (BOOL)resolveInstanceMethod:(SEL)sel {
    NSString *methodName = NSStringFromSelector(sel);
    if ([methodName isEqualToString:@"sendMessage:"]) {
        return class_addMethod(self, sel, (IMP)sendMsg, "v@:@");
    }
    return NO;
}
```

Objective-C中的方法默认被隐藏了两个参数：self和_cmd。self指向对象本身，_cmd指向方法本身。

举两个例子来说明：

例一：`- (NSString *)name`

这个方法实际上有两个参数：`self` 和 `_cmd`。

例二：`- (void)setValue:(int)val`

这个方法实际上有三个参数：`self`, `_cmd` 和 `val`。

这里 `v@:@` 是什么东西呢？实际上，这里的第一个字符 `v` 代表函数的返回类型是 `void`，后面三个字符分别是	`self`, `_cmd`, `msg`，这三个参数的类型 `id`, `SEL`, `NSString`。

（2）如果上述方法返回 `NO`，接着走快速转发

```
// 2. forwardingTargetForSelector 快速转发
- (id)forwardingTargetForSelector:(SEL)aSelector {
    NSString *methodName = NSStringFromSelector(aSelector);
    if ([methodName isEqualToString:@"sendMessage:"]) {
        return [SpareWheel new]; // sendMessage 函数交由 SpareWheel 类处理
    }
    return [super forwardingTargetForSelector:aSelector];
}
```

（3）如果上述方法依然不能处理，将进入第三部

```
// 3. forwardInvocation 慢速转发
// （1）方法签名
// （2）消息转发
- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector {
    NSString *methodName = NSStringFromSelector(aSelector);
    if ([methodName isEqualToString:@"sendMessage:"]) {
        return [NSMethodSignature signatureWithObjCTypes:"v@:@"];
    }
    return [super methodSignatureForSelector:aSelector];
}

- (void)forwardInvocation:(NSInvocation *)anInvocation {
    SEL sel = [anInvocation selector];
    SpareWheel *tempObj = [SpareWheel new];
    if ([tempObj respondsToSelector:sel]) {
        [anInvocation invokeWithTarget:tempObj];
    } else {
        [super forwardInvocation:anInvocation];
    }
    [super forwardInvocation:anInvocation];
}
```

`methodSignatureForSelector:` 的作用在于为另一个类实现的消息创建一个有效的方法签名，必须实现，并且返回不为空的 `methodSignature`，否则会crash。

`forwardInvocation:` 将选择器转发给一个真正实现了该消息的对象。

如果走完 慢速转发 还实现不了，那就走 `doesNotRecognizeSelector:` 方法

```
- (void)doesNotRecognizeSelector:(SEL)aSelector {
    NSLog(@"找不到方法");
}
```

### 2. Method Swizzling 方法交换

Method Swizzling 使我们有办法在程序运行的时候，去修改一个方法的实现，以下是简单实现 lg_reloadData 替换 UITableView 的 reloadData 方法：

```
+ (void)load {
    Method originMethod = class_getInstanceMethod(self, @selector(reloadData));
    Method currentMethod = class_getInstanceMethod(self, @selector(lg_reloadData));
    if (!class_addMethod([self class], @selector(reloadData), method_getImplementation(currentMethod), method_getTypeEncoding(currentMethod))) {
        method_exchangeImplementations(originMethod, currentMethod);
    } else {
        class_replaceMethod([self class], @selector(lg_reloadData), method_getImplementation(originMethod), method_getTypeEncoding(originMethod));
    }
}
```

**说明：**

`reloadData：`被替换方法

`lg_reloadData：`替换方法

`class_addMethod:`如果发现方法已经存在，会失败返回，也可以用来做检查用,我们这里是为了避免源方法没有实现的情况;如果方法没有存在,我们则先尝试添加被替换的方法的实现

如果返回成功：则说明被替换方法没有存在。也就是被替换的方法没有被实现，我们需要先把这个方法实现，然后再执行我们想要的效果，用我们自定义的方法去替换被替换的方法。

如果返回失败：则说明被替换方法已经存在，直接将两个方法的实现交换即。

**为什么不直接用 method_exchangeImplementations 来交换就好？**

因为可能会影响父类中的方法。比如我们在一个子类中，去交换一个父类中的方法，而这个方法在子类中没有实现，这个时候父类的方法就指向了子类的实现，当这个方法被调用的时候就会出问题。所以先采取添加方法的方式，如果添加失败，证明子类已经实现了这个方法，直接用 method_exchangeImplementations 来交换；如果添加成功，则说明没有实现这个方法，采取先添加后替换的方式。这样就能保证不影响父类了。

请到 [GitHub](https://github.com/guoxiaobo-aj/RuntimeDemo) 上查看完整例子。

本文参考：[新手也看得懂的 iOS Runtime 教程](http://www.cocoachina.com/articles/25821)