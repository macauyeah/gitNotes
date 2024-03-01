# Git - 持續整合策略 | Git - Continuous integration strategy

對於原始碼的管理，平常筆者也有在用gitlab的Continuous integration，針對每次提交(commit)，都會有自動編譯和測試。但當一個專案中，有很多關聯庫(dependency library)的引用時，光是專案中每個commit 行auto build就不夠用了。更嚴重的是，若然大家有很多微服務micro service，它們的更新不會反映在commit中。

所以定期重跑動動編譯和測試，是筆者認為可以緩解關聯更新的問題，至少可以提高知道問題所在。

## Night Build的原意
筆者先做了一些功課，參考別人怎樣思考Night build （定期重新編譯）這件事。

1. 每次整合新功能到穩定分支（stable branch）之前，都需要做自動測試。
2. 當專案複雜性越來越大，每次自動測試都把全部測試跑一次，就會遇到效能瓶頸。
3. 所以考慮commit時做單元測試（unit test），然後每個固定的時間問隔做整合測試（integration test）。那個固定的時間間隔就是Night build。

## Night Build之於integration test
筆者原始的問題並不是來自於效能瓶頸，而是涉及關聯性更新問題。這些要麼就有是經code base 層面引發關聯性自動試測，要麼就是Night build重複測試。

這兩個功能，gitlab都有提供，只是筆者初步構想下，Night build比較易設定，也乎合原始的定位。

因為要考慮micro service的於沙盒環境的部署，最簡易的Night build只需要一個共用的環境就夠。但也同樣意味著，Night build需要進行多個不同的分支測試，就需要多個不同的環境。

另外Night build的測時時機也是一個問題，因為測試當下，並不能百份百對應關聯micro services的提交狀況，大家就更需要做好發佈的版本號語意管理。

## Night Build 實務操作上的注意點
Night build第一個要注意的問題，就是要確保同一個commit，真的可以重複建設。一般來說，大家的目標只在運行測試，而自動測試不具破壞性，就基本可以重複的。而如果測試當中包含發佈測試版本，那就還要考慮重複發佈有沒有生效或造成附作用。

以Java maven為例，重複發佈測試版本需要遵守特定的規則，版本號需要以SNAPSHOT結尾，這是為讓maven每天都會重新下載它們的包。而沒有SNAPSHOT結尾的，就只會做一次性下載，減少重複下載造成的資源浪費。若真遇著不支援重複發佈的情況，就需要以日期時間做版本號，就像vscode的某些插件，就是以時間截結尾以作為區分。

Night build另一個要注意的問題，就是開發圖隊何時進行下一輪開發，這會決定何時有新的版本號。扣除上述因為工具不支援的而引發的副作用，還要考慮沒有更新而發生的問題。

有個尷尬情況是，團隊在發佈現行版本時，release commit與main有機會是同一個commit(也就是未有進行下一輪開發)。若不斷重複發佈，有沒有變相發佈了一些沒有預期的功能？例如Docker image，官方大力建議每日自動發佈。當底層的image更新後，頂層引用它們的image，也可以重新發佈，保持安全性。但這樣做的問題，就是頂層的同一個版本號，昨日與今日的運行結果也可能不一樣。這對追蹤問題，並不友好。

所以大家做分支整合時，要預先對版本號作好規劃。然後還要留意Night build不應與release commit重疊。版本號大家做好語意管理，再加上alpha / beta / SNAPSHOT等區分Night build版本，應該就足夠了。而commit重疊問題，就要留意開發週期，Night build要麼就比release早一個commit(即在release時，不推進Night build)，要麼晚一個commit(即馬上規劃下一個版本號進行Night build)。