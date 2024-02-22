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
