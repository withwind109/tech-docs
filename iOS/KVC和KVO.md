KVC（Key-Value Coding）和KVO（Key-Value Observing）是iOS开发中的两种编程技术，用于实现对象属性的间接访问和观察。

KVC 示例代码：

```objc
@interface Person : NSObject
@property (nonatomic, strong) NSString *name;
@property (nonatomic, assign) NSInteger age;
@end

@implementation Person
@end

Person *person = [[Person alloc] init];
[person setValue:@"Alice" forKey:@"name"]; // 设置属性值
[person setValue:@25 forKey:@"age"];

NSString *name = [person valueForKey:@"name"]; // 获取属性值
NSInteger age = [[person valueForKey:@"age"] integerValue];
```

KVC 使用注意事项：

1. 使用正确的键名，否则会抛出`NSUndefinedKeyException`异常。
2. 对象的属性需要遵守KVC编码规范，即有对应的getter和setter方法，或者有实例变量。
3. 可以使用`valueForKeyPath:`来访问嵌套对象的属性。
4. `setValue:forKey:`时，如果传入的值类型不匹配，KVC会尝试进行类型转换，如果无法转换则会抛出异常。

KVO 示例代码：

```objc
@interface Person : NSObject
@property (nonatomic, strong) NSString *name;
@end

@implementation Person
@end

@interface Observer : NSObject
@end

@implementation Observer
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context {
    if ([keyPath isEqualToString:@"name"]) {
        NSLog(@"Name changed: %@", change[NSKeyValueChangeNewKey]);
    }
}
@end

Person *person = [[Person alloc] init];
Observer *observer = [[Observer alloc] init];

[person addObserver:observer forKeyPath:@"name" options:NSKeyValueObservingOptionNew context:NULL];
person.name = @"Bob"; // 观察者会收到通知
[person removeObserver:observer forKeyPath:@"name"]; // 移除观察者
```

KVO 使用注意事项：

1. 必须使用支持KVO的属性，通常是通过`@property`声明的属性。
2. 添加观察者后，一定要在适当的时候移除观察者，否则可能会导致崩溃。
3. 必须实现`observeValueForKeyPath:ofObject:change:context:`方法，以便接收属性变化的通知。
4. `options`参数可以指定观察者接收到的信息，如`NSKeyValueObservingOptionNew`可以获取新值。
5. `context`参数可以传递额外的上下文信息，以区分不同的观察者或观察的属性。