#Mastering Programming
> [Original article link](https://www.facebook.com/notes/kent-beck/mastering-programming/1184427814923414)  
> Translated with the permission from the author, [KENT BECK](https://www.facebook.com/notes/kent-beck/mastering-programming/1184427814923414?comment_id=1185657178133811&comment_tracking=%7B%22tn%22%3A%22R7%22%7D)



From years of watching master programmers, I have observed certain common patterns in their workflows. From years of coaching skilled journeyman programmers, I have observed the absence of those patterns. I have seen what a difference introducing the patterns can make.

這幾年來看了很多專業的程式設計師, 我發現到他們的工作方式都有常見的模式. 這幾年在指導有技巧的初階程式設計師時, 我發現他們沒有這些常見的模式. 我看到這其中的差別, 接著想介紹要這常見的模式是如何建立出來的.

Here are ways effective programmers get the most out of their precious 3e9 seconds on the planet.

有效率的程式設計師會珍惜他們在地球上的3億9千萬秒的時間.

The theme here is scaling your brain. The journeyman learns to solve bigger problems by solving more problems at once. The master learns to solve even bigger problems than that by solving fewer problems at once. Part of the wisdom is subdividing so that integrating the separate solutions will be a smaller problem than just solving them together.

初階的程式設計師藉由一次解決很多問題來解決大問題. 高階的程式設計師藉由一次解決很少的問題來解決大問題. 這其中的智慧就是把大問題細分成數個小問題, 整合這些各別的小問題將會比一次解決一個大問題還要來得簡單.

##Time 時間

###Slicing 細分
Take a big project, cut it into thin slices, and rearrange the slices to suit your context. I can always slice projects finer and I can always find new permutations of the slices that meet different needs.

將一個大專案, 細分成數個小專案, 然後依照你的需求重新排列這些小專案. 我總是可以將這些專案做很好的切割, 當遇到不同的需求, 找到這些專案的新的組合.

###One thing at a time. 一次只做一件事
We’re so focused on efficiency that we reduce the number of feedback cycles in an attempt to reduce overhead. This leads to difficult debugging situations whose expected cost is greater than the cycle overhead we avoided.

我們非常專注效能, 就是我們試著減少開銷來降低回饋週期的次數, 這會導致處於一種困難的除錯情境, 就是預期花費大於我們避免的週期開銷.

###Make it run, make it right, make it fast. 讓他運行, 正確, 快速
(Example of One Thing at a Time, Slicing, and Easy Changes)


###Easy changes. 
When faced with a hard change, first make it easy (warning, this may be hard), then make the easy change. (e.g. slicing, one thing at a time, concentration, isolation). Example of slicing.

當我們面臨到困難的改變, 先要把它變成簡單 (注意, 這也許非常困難), 接著要做簡單的改變. (例如, 切割成一次就可以完成的事, 集中, 獨立)

###Concentration. 集中
If you need to change several elements, first rearrange the code so the change only needs to happen in one element.

如果你需要一次改變很多元件, 先重構程式碼, 使得改變需求時, 只要改一個元件.

###Isolation. 獨立
If you only need to change a part of an element, extract that part so the whole subelement changes.

如果你只需要改變一個元件的一部份, 把這部分抽離出來使得只要修改這個子元件.

###Baseline Measurement. 
Start projects by measuring the current state of the world. This goes against our engineering instincts to start fixing things, but when you measure the baseline you will actually know whether you are fixing things.

在專案開始時, 測量真實環境現在的狀態, 會違反我的工程師開始確定一件事的本能, 但當你測量基礎線時, 你將會真正知道你已經確定這些事.

##Learning
###Call your shot. 

Before you run code, predict out loud exactly what will happen.

在執行程式前, 先大膽的預測將會發生什麼事.

###Concrete hypotheses.
When the program is misbehaving, articulate exactly what you think is wrong before making a change. If you have two or more hypotheses, find a differential diagnosis.

當程式不合預期時, 在做修改前, 明確的表達你的想法是錯的. 如果你有兩種或種以上假設, 找出其中的差別並診斷分析.


###Remove extraneous detail.
When reporting a bug, find the shortest repro steps. When isolating a bug, find the shortest test case. When using a new API, start from the most basic example. “All that stuff can’t possibly matter,” is an expensive assumption when it’s wrong.
E.g. see a bug on mobile, reproduce it with curl

當回報出錯誤時, 找到最短的步驟來重現. 當把錯誤獨立出來, 找到最短的測試案例. 當使用新的 api 時, 從最基本的範例開始. “所有的東西都不可能錯” 當真的錯的時候, 這是一句很昂貴的假設.
例如, 在手機上發現錯誤, 用 curl 重現他.

###Multiple scales. 
Move between scales freely. Maybe this is a design problem, not a testing problem. Maybe it is a people problem, not a technology problem [cheating, this is always true].

在規模裡自由的移動. 也許這是一個設計的問題, 不是一個測試的問題. 也許這是人的問題, 不是技術的問題 [開玩笑的, 但總是事實]

##Transcend Logic

###Symmetry. 
Things that are almost the same can be divided into parts that are identical and parts that are clearly different.

幾乎所有事件都可以被分割成很多部分, 這些部分都是很明確的不一樣.


###Aesthetics. 
Beauty if a powerful gradient to climb. It is also a liberating gradient to flout (e.g. inlining a bunch of functions into one giant mess).


###Rhythm. 
Waiting until the right moment preserves energy and avoids clutter. Act with intensity when the time comes to act.


###Tradeoffs. 
All decisions are subject to tradeoffs. It’s more important to know what the decision depends on than it is to know which answer to pick today (or which answer you picked yesterday).


##Risk

###Fun list. 
When tangential ideas come, note them and get back to work quickly. Revisit this list when you’ve reached a stopping spot.

當靈感一來, 記下來然後快速地回到工作上. 當你空閒下來時, 回來查看這些清單.

###Feed Ideas.
Ideas are like frightened little birds. If you scare them away they will stop coming around. When you have an idea, feed it a little. Invalidate it as quickly as you can, but from data not from a lack of self-esteem.

靈感就像受驚嚇的小鳥, 如果你嚇跑牠們, 牠們就不會再來. 當你有這些靈感, 一點一點的品嚐它. 

###80/15/5. 
Spend 80% of your time on low-risk/reasonable-payoff work. Spend 15% of your time on related high-risk/high-payoff work. Spend 5% of your time on things that tickle you, regardless of payoff. Teach the next generation to do your 80% job. By the time someone is ready to take over, one of your 15% experiments (or, less frequently, one of your 5% experiments) will have paid off and will become your new 80%. Repeat.

把 80% 的時間拿來從事低風險/合理報酬的工作. 把 15% 的時間拿來從事高風險/高報酬的工作. 剩下 5% 的時間拿來從事會讓你開心且不在意是否有報酬的工作. 訓練你的下一代來做你 80% 的工作. 等到有人準備好代替你, 

##Conclusion
The flow in this outline seems to be from reducing risks by managing time and increasing learning to mindfully taking risks by using your whole brain and quickly triaging ideas.
