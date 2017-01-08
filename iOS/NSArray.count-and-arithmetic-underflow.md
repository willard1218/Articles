NSArray.count and arithmetic underflow
==


```objective-c
NSMutableArray *arr = [NSMutableArray array];

int i = 1;
if (i < arr.count - 1) {
  [arr removeObjectAtIndex:i];
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
		
根據蘋果文件的 [NSArray count](https://developer.apple.com/reference/foundation/nsarray/1409982-count?language=objc) 的解說，發現 count 這個方法是回傳 NSUInteger 型態的數值，於是再做了個實驗：

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
   
 