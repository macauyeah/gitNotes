# github flow - github 開發流程

## 那些年那個很穩定卻又不受歡迎的 git flow 開發流程
多年前，朋友就向筆者介紹git的團隊整操作流程。筆者深思過後，的確實用，那些年的git-flow，很美滿，由開發、測試，到發佈、修補漏動（backport），都有清楚明確的指引。

原作者連結：[git-flow](https://nvie.com/posts/a-successful-git-branching-model/)

大家如果沒有更複雜的需求，真的可以照搬，筆者也很推這一個模型。

但在長期推廣下，筆者發現大部份人其實都不熟git的基本操作，什至連git graph也不看，現在看git flow，就更不可能接受。那怕是有常用git的個人團隊，也是不怎使用分支模型。

前一兩年，筆者也不懂，筆者也努力地簡化git flow。例如把master和develop合而為一，但最後也是少有人可以接受，很多人還是卡在分支那邊，對checkout、merge還是很陌生。在跟更多不同人的協作過後，筆者總於意會到一件事。其實大部份人，只想知道最後、最新的狀態，只會更新 master / main ，也因為個人開發，所以連衝突也不會有，更不需要使用merge。那怕是少型團隊，頂多也是維護main的衝突，間中用用merge，而checkout還是用不著。

其實這個情況，並不限於小型團隊。因為 web app 和 DevOps 的流行，所以越來越少機會要維護多個舊的穩定版本。大家都專心於最後一個開發及發佈版本就完事，用戶的某個版本有問題？更新到最新版本吧。（註：越底層的應用開發模式，因為相容性問題，不可能只保留一個穩定版本。）

## 那麼我們就大力簡化吧 - github flow 開發流程
既然大部份情況，大家都只在乎 main / master / 預設分支，那我們也沒有必要跟著複雜的 git flow 走。但在 DevOps 的角度下，為保證 main / master 穩定性，大家還是至少要遵守branching 、pull (merge) request 、code review 、auto test 原則 。

github就最簡單的branching 、 pull request 、 code review 提出了它們的 [github flow doc](https://docs.github.com/en/get-started/using-github/github-flow), [github flow](https://githubflow.github.io/)。

簡而言之，就是每個人在開發時，都先從 main 起一個新分支，不斷更新。待合適的時候，就透過 pull requst，向原項目負責人提出申請，只要項目負責人點頭，就可以把改動傳入 main 中。又因為Github 原本的定位在於個人與個人之間的協作，初時已經需要通過fork建立獨立的倉庫，那怕你不愛分支也必需分支。所以 pull request，code review 的作用更明顯，後逐的協作更理所當然。

但若果回到公司團隊協，Github flow 就應該像筆者之前提出協作方案，各自起分支，最後由某個人守門，把所有結果放到 main 中。（[前文連結](gitcoworkflow.md)）

## Github flow 沒有提及的發佈 - 佈署 | Release - Deployment
在上文中，經過 pull request 、 code review 、 auto test ，道理上，開發者可以做的都已經做過了，然後就是等待發佈 - Release。

對於單純的庫類型的程式碼，筆者認為，的確沒有事可以再做，實務上就是直接找人其他程多員試用最新版本，看看有沒有問題。只要 main / master 上，明確的表示版本號的變更，就差不多等於直接發佈。有需要提供binary版本的，就還需要觸發上載binary的流程，但這個跟 pull request 觸發 auto test 差不多， auto test 成功後就上載。

對於服務類型的程式碼，例如 Web App 等，直接發佈到正式環境還是有些不妥吧？始終會即時影響到業務，我們至少有個測試場，經用戶做實際的業務操作去驗收。但這個時機，應該是在Github flow的什麼時候做？

在原始的git flow中，有一個叫做 develop 的相對穩定分支，僅次於 main 。它是功能開發完成後第一次pull request 的地方，我們可以用這個概念來做自動發佈到測試場。但若在github flow 中加入了這個 develop / uat / staging 分支，其實就等於複雜地回到過去傳統的 git flow中，對好多新手來講難以接受。Github flow 的成功簡化，其實很大依賴著自動化測試。現在的測試用例，並不再限於單元測試。就連整合測試，也可以經Docker等容器化技術去做，只要我們的自動化測試有足夠信心，就可以發佈。但反觀我們的 Web App 例子，我們認為自動化測試難似涵蓋所有情境，也難以開發。所以我們還在有個時間發佈到測試場，進行人工測試。

### pull request + 快速迭代
筆者結合自己的經驗，配上國外討論區 Stack overflow 的內容，筆者認為Github flow上進行 pull request 後，就是最好的發佈測試場時機。所以我們需要盡快進行驗收測試，完成後在Git commit上加上Tag，以示通過驗收測試，可以發佈正式環境的版本。

不過這個模式是有一個很重要的前題假設：快速迭代。當我們驗收完成後，盡可能快地發佈到正式環境，不然會阻礙下一個功能的pull request驗收，或是覆蓋了上一個pull request的驗收環境。

用反面的例子來說明，如果我們有很多功能需要驗收，或變化很多，或存在多輪的里程碑開發，我們就不適宜那上述模式。最保險的做法，還是回到傳統的 git flow ，引入 develop / uat / staging 分支。但如果大家還是那麼討厭傳統 git flow，筆者還是有另一個提議。

### 既不想回到傳統 git flow ，但又需要慬㥀的考慮驗收發佈流程
如果開發的功能變化比較大，需要多方面協調、測試、驗收，經歷多次里程碑後，才有一個對外發佈的版本，大家可以考慮分開 Repository 做開發。例如: v1，v2的 Repository 完全獨立。 v1 是已發佈的版本，有獨立的測試場，任何即時候需要修正，就在v1的 Repository 做 pull request。 v2 則是未發佈版本，亦有獨立的測試場。加入任何新功能後，就在v2的 Repository 做 pull request，用自己專用的測試場做驗收。到 v2 正式發佈後， v1 就封存處理，再開一個 v3 作為下一個大版本的開發。這個模式，那怕在庫類型的程式碼也用得上。

這樣做的好處是 git Repository 和歷史記錄都會獨立，自動發佈的腳本程也會簡單明確一些。壞處則是 v1 v2 難以做功能對比，我們只能靠人腦記著 v1 有沒有什麼後期加入的修正和功能，需要同步移植到 v2 中 (相對的，著是同一個Repository，可以利用merge 功能確保 v1 有的，v2 都己處理，只是必需要很懂處理版本衝突問題。