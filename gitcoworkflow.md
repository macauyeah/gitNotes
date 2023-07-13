# Git Co-Work Flow
雖然git面世已很久，但相當一部份澳門朋友都是solo man，很少合作寫code，對git branch始終都有些恐懼。所以這次來解召一個基本原則，至少你不會爛了code救不回來。

若然大家未熟悉git，初次利用git合作寫program，請盡量減少使用共同分支(branch)，可以極大地減少問題。

## 第一個大原則 - 建立一條自己分支
在一個repo中，為自己建立一條分支(branch)，可以減少Remote repo中有人比你先commit，而令你push失敗的情況。
```bash
git clone xxx
git checkout -b YOUR_BRANCH

# 提交你的改動
git add FILE_OR_FOLDER
git commit
# 上傳
git push origin YOUR_BRANCH
```

除非你的隊友故意你用的分支名先commit，又或者你自己有幾台電腦，幾台一起做改動。不然push 應該不會有問題。

## 第二個大原則 - 用fetch取代pull
很多人在取用Remote Repo的更新時，都會使用pull。但pull其實是fetch及merge的混合，而且merge還要考慮source branch是那條分支的問題，若然大家都有一條獨立branch，那麼這個無腦pull並不存於每人只有一台電腦下的多人協作中。

fetch的過程中，還可以加入參數--prune，順便依照Remote Repo的指示，同步刪掉本機中一些不再存在的origin/branch。

```bash
# 不要用pull，用fetch看看server的最新改動
git fetch origin --prune
# 跟上述command類似，只是在fetch的途中，把main和 origin/main自動同步
git fetch origin main:main --prune
```

## 第三個大原則 - Merge前先Commit
經過前述fetch後，其實他人的改動並未加入自己的分支中，必需經過merge才會出現。但並不是沒有conflict就無腦merge。 假若自己有改動，未commit，應該老虎蟹都先commit。這是為了在merge後，還有機會可以無腦reset，回到之前那個commit。這就像是做任何更新前，先做backup。

```bash
# 看看有沒有未commit的改動，若有，先commit。
git status
git add FILE_OR_FOLDER
git commit

# 把別人的改動加到自己的分支中, 若有衝突請，git會提示你去修正
git merge origin/OTHER_BRANCH

# 檢查merge後的code有沒有問題，沒有就可以上傳
git push origin YOUR_BRANCH
```

## 第四個大原則 - 由某個特定的人來管理master或main branch
main branch(以前叫master branch)，是他人下載時的預設分支，也是Github、Gitlab的預設顯示分支。所以該分支存放著的source code，應該在代表信心度比較高。

在協作的環境中，每人都有自己分支，那就代表要有一位人員做管理，他負責checkout main, 然後合併其他已驗證的分支。

```bash
# 用fetch看看server的最新改動
git fetch origin --prune

# 看看自己分支有沒有未commit的改動，若有，先commit。你不應在main中改動，否則後述部份會有機會亂了。
git status
git add FILE_OR_FOLDER
git commit

# Optional，切換到他人的分支，執行程式以驗證更新
git checkout origin/SOMEONE_BRANCH
# 檢視差異，若發現有任何問題，例如他的分支落後於main，或很大機會出現衝突，可以叫對方先更新到最新狀況，再重來一次
git diff main origin/SOMEONE_BRANCH

# 有信心，就可以合併到main中
git checkout main && git merge origin/SOMEONE_BRANCH
git push origin main

# 回到你原來的分支中
git checkout YOUR_BRANCH
```

在某些比較嚴僅的環境中(例如Github、Gitlab)，main分支可能會被系統機制鎖定，必需通過系統內建的Pull Request(或Merge Request)，才能通過審核，合併到main。另外，也有一些關於開發上的[Git workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)，主要針對功能管理、版本發佈、錯誤修正等控制。有機會再為大家介紹。

希望以上的流程，可以有效且容易地讓大家協作。如果有任何command錯誤或更新，都可以經Github Pull Request通知筆者。