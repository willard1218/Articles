#Mastering Programming
#精通程式設計
> [Original article link](https://www.facebook.com/notes/kent-beck/mastering-programming/1184427814923414)  
> Translated with the permission from the author, [KENT BECK](https://www.facebook.com/notes/kent-beck/mastering-programming/1184427814923414?comment_id=1185657178133811&comment_tracking=%7B%22tn%22%3A%22R7%22%7D)



From years of watching master programmers, I have observed certain common patterns in their workflows. From years of coaching skilled journeyman programmers, I have observed the absence of those patterns. I have seen what a difference introducing the patterns can make.

這幾年來觀察了很多大師級的程式設計師，發現他們的工作流程中有一些共通的模式。幾年來在輔導很多有水準的資深碼工（程式設計師）時，卻沒有觀察到這些模式。我已見證導入這些模式能帶來多大的差別。

Here are ways effective programmers get the most out of their precious 3e9 seconds on the planet.

接下來談談有效率的程式設計師如何好好運用他們生活在地球上珍貴的 30 億秒（譯註：約等於95年）。

The theme here is scaling your brain. The journeyman learns to solve bigger problems by solving more problems at once. The master learns to solve even bigger problems than that by solving fewer problems at once. Part of the wisdom is subdividing so that integrating the separate solutions will be a smaller problem than just solving them together.

這裡的主旨是擴展你的思維方式。工匠學著解決盡量多的問題以便一次性地解決大問題；大師則試著解決其中較少量的問題，來解決更龐大的問題。這其中的智慧是，如何切分問題點，以便輕易地整合不同的解法；這會比一次解決全部的問題還要來得輕鬆。

##Time 時間

###Slicing 細分
Take a big project, cut it into thin slices, and rearrange the slices to suit your context. I can always slice projects finer and I can always find new permutations of the slices that meet different needs.

將一個大專案細分成數個小切片，並且重新排列以符合你的現況。我隨時可以將這些專案再切得更細小，而且還能重新組合來面對不同的需求。

###One thing at a time. 一次只做一件事
We’re so focused on efficiency that we reduce the number of feedback cycles in an attempt to reduce overhead. This leads to difficult debugging situations whose expected cost is greater than the cycle overhead we avoided.

由於我們相當注重效率，因此我們總是試圖減少回饋週期的次數以避免重新檢視的無謂開銷。但這往往反而導致了偵錯難度的提升，其付出的成本常大於原本想避免回饋週期所產生的開銷。

###Make it run, make it right, make it fast. 讓它動起來，正確，快速
(Example of One Thing at a Time, Slicing, and Easy Changes)
（「細分」、「一次只做一件事」、「簡化更動」的例子）


###Easy changes. 簡化更動
When faced with a hard change, first make it easy (warning, this may be hard), then make the easy change. (e.g. slicing, one thing at a time, concentration, isolation). Example of slicing.

當我們面對困難的更動，首先要進行簡化（警告，這可能很難），然後再實施簡化後的更動。（例如：「切割」、「一次只做一件事」、「集中」、「離析」等原則）。這是「切割」原則的一個例子。

###Concentration. 集中
If you need to change several elements, first rearrange the code so the change only needs to happen in one element.

如果你需要一次修改很多元件，先重新整理程式碼（譯註：或重構），使得只需要修改一個元件。

###Isolation. 離析
If you only need to change a part of an element, extract that part so the whole subelement changes.

如果你只需要改變一個元件的一部份，把這部分抽離出來使得只要修改這個子元件。

###Baseline Measurement. 基準測量
Start projects by measuring the current state of the world. This goes against our engineering instincts to start fixing things, but when you measure the baseline you will actually know whether you are fixing things.

在專案開始時先考量真實環境的狀態。這違背工程師馬上動手修東西的本能，但是有了基準線之後，你才知道你真的有把東西修好。

##Learning
###Call your shot. 當家作主

Before you run code, predict out loud exactly what will happen.

在執行程式前，先大膽的預測將會發生什麼事。

###Concrete hypotheses. 具體假設
When the program is misbehaving, articulate exactly what you think is wrong before making a change. If you have two or more hypotheses, find a differential diagnosis.

當程式執行不合預期時，在做修改前，先明確指出你覺得什麼事做錯了。如果你有兩種以上的假設，分析這些假設有哪些差異。


###Remove extraneous detail. 移除外部細節
When reporting a bug, find the shortest repro steps. When isolating a bug, find the shortest test case. When using a new API, start from the most basic example. “All that stuff can’t possibly matter,” is an expensive assumption when it’s wrong.
E.g. see a bug on mobile, reproduce it with curl

當回報出錯誤時，找出最簡短的重現步驟。當把錯誤離析出來後，找到最短的測試案例。當使用新的 API 時，從最基本的範例開始。「這些部分應該無關緊要吧」，錯誤假設的代價只有出事的時候才能察覺。
舉例：在手機上發現錯誤，用 curl 重現他。

###Multiple scales. 多重規模
Move between scales freely. Maybe this is a design problem, not a testing problem. Maybe it is a people problem, not a technology problem [cheating, this is always true].

時時在不同尺度中遊走。「這也許是設計問題，而不是測試問題」。「也許這是人的問題，而不是技術問題」（這有點作弊，因為永遠都是人的問題）。

##Transcend Logic 超越邏輯

###Symmetry. 對稱性
Things that are almost the same can be divided into parts that are identical and parts that are clearly different.

幾乎等同的事物，可以被切割成重複到的部分跟完全不一樣的部分。


###Aesthetics. 美感
Beauty is a powerful gradient to climb. It is also a liberating gradient to flout (e.g. inlining a bunch of functions into one giant mess).

「美感」是一個難以攀爬、有影響力的梯度，同時也是一個常被嘲笑忽視的、奔放的梯度（例如：行內定義一堆函數然後變得一團亂）。



###Rhythm. 節奏
Waiting until the right moment preserves energy and avoids clutter. Act with intensity when the time comes to act.

等待時機，儲備精力，避免混亂。時機成熟時，將爆發力化為行動。


###Tradeoffs. 折衷
All decisions are subject to tradeoffs. It’s more important to know what the decision depends on than it is to know which answer to pick today (or which answer you picked yesterday).

所有決策都有可能要面對折衷。更重要的是去了解這些決策取決於什麼，而不是今天（或昨天）選擇了哪個選項。


##Risk 風險

###Fun list. 趣事清單
When tangential ideas come, note them and get back to work quickly. Revisit this list when you’ve reached a stopping spot.

當離題的靈感一來，記下來然後快速地回到工作上。當你空閒下來時，再回來查看這些清單。

###Feed Ideas. 餵養靈感
Ideas are like frightened little birds. If you scare them away they will stop coming around. When you have an idea, feed it a little. Invalidate it as quickly as you can, but from data not from a lack of self-esteem.

靈感就像受驚嚇的小鳥，如果你嚇跑牠們，牠們就不會再來了。當你有靈感時，給他一點時間。即時拋棄不可行的點子，但是用數據說話，而不是因為單純沒自信而這麼做。

###80/15/5.
Spend 80% of your time on low-risk/reasonable-payoff work. Spend 15% of your time on related high-risk/high-payoff work. Spend 5% of your time on things that tickle you, regardless of payoff. Teach the next generation to do your 80% job. By the time someone is ready to take over, one of your 15% experiments (or, less frequently, one of your 5% experiments) will have paid off and will become your new 80%. Repeat.

把 80% 的時間拿來從事低風險／合理報酬的工作。把 15% 的時間拿來從事高風險／高報酬的有關實驗。剩下 5% 的時間拿來從事會讓你開心且不在意是否有報酬的實驗。訓練新手來做你 80% 的工作，等到有人準備好代替你，你那 15% 的實驗（或者，你那 5% 的實驗，雖然很少見）就能收成而成為你新的 80%。不斷重複這個過程。

##Conclusion 結論
The flow in this outline seems to be from reducing risks by managing time and increasing learning to mindfully taking risks by using your whole brain and quickly triaging ideas.

這份綱要中的流程說明了：從透過管理時間、加強學習來降低風險，到用你的大腦來謹慎地承擔風險，還有快速分類你的靈感。
