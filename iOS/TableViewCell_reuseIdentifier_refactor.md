# 從 TableViewCell reuseIdentifier 探討重構這件事

在 iOS 裡，有以下兩種 UI 元件：

1. `TableViewCell`
2. `TableView`


此兩種元件相輔相成，`TableView` 是一種用來顯示表格的佈局方式；  
而 `TableViewCell` 則是表格裡面每行 (row) 要顯示什麼內容。

我們經常會要客製化 `TableViewCell`，  
必要時，更會使用新增類別，來繼承 `TableViewCell` 的方式，  
來達到 *重複使用* 的目的。

如果今天有一個 **TodoList** 的 App，  
用途是使用者可以在畫面上新增、修改、刪除、查詢待辦事項，  
畫面一開始一定是列出所有待辦事項，就會出現一個 `TableView`，  
表格的每一行，我會新增一個類別：`TodoItemCell` 來繼承 `TableViewCell`。

在 iOS 裡，因為 `TableView` 是可以捲動的，  
如果今天項目太多，為了避免造成一次建立太多 `TableViewCell`，  
會有一個 `ReuseIdentifier` 來跟你說現在可以重複使用哪一個 `TableViewCell`，  
你只要重新改變這個 `TableViewCell` 的樣子，  
而不用重新建立，節省記憶體空間。


程式一般如下寫：

## 一般寫法

```objective-c
- (void)viewDidLoad {
    // do something ...
    [self.tableView registerClass:TodoItemCell.class forCellReuseIdentifier:@"cellId"];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    TodoItemCell *cell = [tableView dequeueReusableCellWithIdentifier:@"cellId" forIndexPath:indexPath];
    // do something ...
    return cell;
}
```


問題來了：*因為兩邊使用到 `ReuseIdentifier` 都是寫死，*  
*很可能改一個地方忘記改另外一個地方。*

## 重構 - 將共同的抽離出來

```objective-c
const NSString *kTodoItemCellIdentifier = @"TodoItemCellIdentifier";
- (void)viewDidLoad {
    // do something ...
    [self.tableView registerClass:TodoItemCell.class forCellReuseIdentifier:kTodoItemCellIdentifier];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    TodoItemCell *cell = [tableView dequeueReusableCellWithIdentifier:kTodoItemCellIdentifier forIndexPath:indexPath];
    // do something ...
    return cell;
}
```

改成這樣後，不會再發生改下面忘記改上面的 `ReuseIdentifier` 的情形發生，  
但問題來了：*如果我今天這個 `TodoItemCell` 要給其他類別使用呢？*

有些人可能會想到，將 `kTodoItemCellIdentifier` 放到一個 Constant.h，  
好讓其他類別能夠使用，  
但問題來了：*每個 XXXCell，你都要在宣告一次常數，*  
*日後 Constant.h 會隨著專案越來越大而越來越多行，變得更難維護。* 

我的解法是，宣告在 `UITableViewCell` 的 **catrgory**，  
增加一個 **class method** : `- (NSString *)Identifier`，  
好讓各式各樣不同的 Cell 繼承他時，就自動具備了這樣的功能，  
實作如下：


## 重構 - 在 UITableViewCell 的 Catrgory

```objective-c
@interface UITableViewCell (Helpers)
+ (NSString *)identifier;
@end


@implementation UITableViewCell (Helpers)
+ (NSString *)identifier {
    NSString *reuseIdentifier = [NSString stringWithFormat:@"%@Identifier", NSStringFromClass(self.class)];
    return reuseIdentifier;
}
@end
```

如此一來，因為 `TodoItemCell` 繼承 `UITableViewCell`，  
所以只要使用 `[TodoItemCell identifier]` 就可以拿到我想要的東西，  

因為會使用目前的 class 名字，產生動態的 `ReuseIdentifier`，  
所以具備唯一性，  
即使以後新增 `XXXCell`、`YYYCell`，  
也不需要重新定義常數 `kXXXCellIdentifier`、`kYYYCellIdentifier`，     
藉以達到 **Write once, run anywhere**。

### 後記
此篇文章分享在 [Swift Taiwan](https://www.facebook.com/groups/swifttw/permalink/1487577911254580/) 後，有網友分享更方便的 [寫法](http://qiita.com/tattn/items/bdce2a589912b489cceb#uitableview)，  
已將內文中的 Swift 版本改成 Objectice-c：


## 重構 - 寫在 UITableView 的 Category
```objective-c
// UITableView+Helper.h

#import <UIKit/UIKit.h>

@interface UITableView (Helper)

- (void)registerClass:(_Nonnull Class)cellClass;

- (void)registerClassList:(NSArray <Class> *_Nonnull)cellClassList;

- (__kindof UITableViewCell *_Nonnull)dequeueReusableCellWithClass:(_Nonnull Class)cellClass forIndexPath:(NSIndexPath *_Nonnull)indexPath;

@end
```


```objective-c
// UITableView+Helper.m
#import "UITableView+Helper.h"

@implementation UITableView (Helper)

- (void)registerClass:(_Nonnull Class)cellClass {
    NSString *reuseIdentifier = NSStringFromClass(cellClass);
    [self registerClass:cellClass forCellReuseIdentifier:reuseIdentifier];
}


- (void)registerClassList:(NSArray <Class>*)cellClassList {
    for (Class class in cellClassList) {
        [self registerClass:class];
    }
}

- (__kindof UITableViewCell *_Nonnull)dequeueReusableCellWithClass:(_Nonnull Class)cellClass forIndexPath:(NSIndexPath *_Nonnull)indexPath {
    return [self dequeueReusableCellWithIdentifier:NSStringFromClass(cellClass) forIndexPath:indexPath];
}

@end
```

相較於原本的寫法，現在使用更方便了：

```objective-c
- (void)viewDidLoad {
    // do something ...
    
    [self.tableView registerClass:TodoItemCell.class forCellReuseIdentifier:[TodoItemCell Identifier]];
    // 重構 - 在 UITableViewCell 的 Catrgory
    
    [self.tableView registerClass:TodoItemCell.class];
    // 重構 - 寫在 UITableView 的 Category
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    TodoItemCell *cell = [tableView dequeueReusableCellWithIdentifier:[TodoItemCell Identifier] forIndexPath:indexPath];
    // 重構 - 在 UITableViewCell 的 Catrgory
    
    TodoItemCell *cell = [tableView dequeueReusableCellWithClass:TodoItemCell.class forIndexPath:indexPath];
    // 重構 - 寫在 UITableView 的 Category
    
    // do something ...
    return cell;
}
```

可以看出有以下好處：
1. 註冊 cell 的時候，從兩個參數縮減為一個。
2. 隱藏了 `ReuseIdentifier`，讓介面統一使用 `TodoItemCell.class` 當參數。