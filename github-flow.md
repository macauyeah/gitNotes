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

github就最簡單的branching 、pull  request 、code review 提出了它們的 [github flow](https://githubflow.github.io/)。

簡而言之，就是每個人在開發時，都先從 main 起一個新分支，不斷更新。待合適的時候，就透過 pull requst，向原項目負責人提出申請，只要項目負責人點頭，就可以把改動傳入 main 中。又因為Github 原本的定位在於個人與個人之間的協作，初時已經需要通過fork建立獨立的倉庫，那怕你不愛分支也必需分支。所以 pull request，code review 的作用更明顯，後逐的協作更理所當然。


但若果回到公司團隊協，Github flow 就應該像筆者之前提出協作方案，各自起分支，最後由某個人守門，把所有結果放到 main 中。（[前文連結](gitcoworkflow.md)）

