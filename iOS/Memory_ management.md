
##Memory management 

先宣告一個 People 的物件

```objective-c
@interface People : NSObject
@property NSUInteger age;
@end

@implementation People
- (void)dealloc {
    NSLog(@"%s",__PRETTY_FUNCTION__);
}
@end
```

在另一個物件宣告成 Property：

```objective-c
@property (weak) People *weakPeople;
@property (strong) People *strongPeople;
@property (assign) People *assignPeople;
```

並在某個函式使用：

```objective-c
if (true) {
    People *people1 = [[People alloc] init];
    _weakPeople = people1;
    
    People *people2 = [[People alloc] init];
    _strongPeople = people2;
    
    People *people3 = [[People alloc] init];
    _assignPeople = people3;
}
```

在結束 if 這個 block 時：

| | _weakPeople | _strongPeople | _assignPeople |
| --- | --- | --- | --- |
| 是否有被 retain | ✕ | ✔ | ✕ |
| 值 | nil | 有效的值 | 垃圾值 |
| 備註 | | | 拿 _assignPeople.age 會 crash |

