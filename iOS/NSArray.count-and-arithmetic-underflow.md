NSArray.count and arithmetic underflow
==

需求
===
在走訪 array 時，要把 arr[i]、arr[i+1] 做比較，如果相等就要刪除兩個；否則，就繼續走訪。
 
程式如下：

```objective-c
NSMutableArray *arr = [NSMutableArray arrayWithArray:@[@2,@2]];
    
int i = 0;
while (i < arr.count -1) {
    bool removeTwoItems = [arr[i] intValue] ==
                          [arr[i+1] intValue];
    if (removeTwoItems) {
        [arr removeObjectAtIndex:i];
        [arr removeObjectAtIndex:i];
    } else {
        i++;
    }
}
```

你覺得這段 code 有沒有問題？

如果我告訴你，這 code 會噴 *NSRangeException*，你信不信？為什麼？


* .
* .   
* .
* .
* 思考時間
* .
* .
* .
* .
* .  







	
		*** Terminating app due to uncaught exception 'NSRangeException', reason: '*** -[__NSArrayM objectAtIndex:]: index 0 beyond bounds for empty array'
		
	
下斷點發現看是在第二次走訪時，array 已經是空的，卻還從裡面拿東西，就在想為什麼會這樣，做了個小實驗：

```objective-c
NSMutableArray *arr = [NSMutableArray array];
    
int i = 0;
if (i < arr.count - 1) {
    NSLog(@"0 < -1");
}
else {
    NSLog(@"0 >= -1");
}
```
    
卻發現會執行 if 區塊的 Log，跑去看蘋果文件的 [NSArray count](https://developer.apple.com/reference/foundation/nsarray/1409982-count?language=objc) 的解說，發現 count 這個方法是回傳 NSUInteger 型態的數值，於是再做了個實驗：

```objective-c
NSMutableArray *arr = [NSMutableArray array];
   
NSInteger i = arr.count - 1;
// i = -1
NSUInteger j = arr.count - 1;
// j = 18446744073709551615
```
    
結論
===
因為 NSUInteger 的範圍是 0 ~ 18446744073709551615，
故如果是最小值 - 1 就會變成最大值；如果是最大值 + 1 就會變成最小值。
   
 