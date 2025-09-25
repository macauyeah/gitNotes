# git submodule
初次實務上使用submodule來同時管理幾個project的更新。如果有任何理解上的錯誤，請在github中提issue或pull request。

# 為何要使用 submodule
假設你的團隊中有三個人，A君做A Project，B君做B Project，C君做Main Project。如果可以，A，B各提供已編譯的Binary或Library，給C君直接使用就最好。

但要做到好好管理，A，B都要有自己的發佈系統，即是把Binary上傳到某個分享Repo中，這樣C君就能有條理地通過IDE或Compile工具下載對應的版本。如果是javascript，Repo可能就是npm repo，如果是java，可能就是maven repo。這亦代表A，B君對程式編譯、打包、版本命名等都要很熟悉，不能一輩子都命名為v1.0.0。

如果團隊對這些都不熟悉，C君還有什麼方法呢?其實靠著Submodule的功能，C君也可以硬把A，B的Source code取出，做最後打包。

這跟A、B君自己把source code壓縮然後Email寄給C君是有不同的。因為這樣C君並不清楚A，B的git脈絡：C君需要自己做好A、B的版本記錄。想要只回滾A，B的版本普不容易。但經過git Submodule後，C君可以清楚知道現在正使用的是A、B的那一個commit版本。假如有一天，A、B、C三個都更新了，但發現合起來時就跑不動。C君可以保持A、C的版本不變，單獨提取B的某個版本進行測試。當然，你可以說原本Email也可以這樣管理，但始終你不清楚B的版本記錄，Email的日期並不代表Source Code的進度。(因為有時候,Bug Fix是針對舊版本的做更新，新功能的Email日期反而比Bug Fix要早)

同理，如果大家要連結多個沒有發佈系統的文字資料，也可以利用Submodule。例如筆者正在編輯一[本書](https://macauyeah.github.io/AProgrammerPrepares/)，當中不同的主題，就是使用Submodule的功能串連起。

# 指令 Command
馬上看來來Submodule可以怎樣做。 假設你已經知道git 怎樣用，也起了git repo。假設你是C君，進入你的本機repo資料夾內，使用submodule參數。
```bash
cd SOMEWHREE_IN_MAIN_FOLDER
git submodule add SUBMODLE_REPO_LINK
cd MAIN_FOLDER
git add -u && git commit -m 'Add sub module'
```

上面的效果，就是把C君當前repo的狀態，連結到B君submodule當時預設分枝(default branch)的最後一個commit 中。然後C君在自己的repo怎樣更新，它引用到B君的submodule版本都不會變。

直到某一刻，B君說他加了一個穩定的新功能，請C也連帶更新一下。C君也做好自己的準備後，使用submodule參數進行更新。
```bash
cd MAIN_FOLDER
git submodule update --remote
# run any unit test, integration test, confirm that it works after submodule update, then commit
git add -u && git commit -m 'update sub module and auto checkout'
```
注意，如果C君有多於一個submodule，上述指令會全部一口氣更新。另外，如果你覺得B君的最新版本不能用，還是可以針對B君取得特定的版本。

```bash
cd SUBMODULE_FOLDER
git checkout SUBMODULE_COMMIT
cd MAIN_FOLDER
git add -u && git commit -m 'update sub module to specific commit'
```

如果，你是D君，直接下載A君的多module project，那在初次下載時，submodule還是空的，需要先init，再update。
```bash
git clone ...
ls SUBMODULE_FOLDER
# SUBMODULE_FOLDER will be empty
cd MAIN_FOLDER
git submodule init
git submodule update
ls SUBMODULE_FOLDER
# submodule code should be there
```

如果你同時是A,B,C君，其實多個module也是你自己管理的，你可以直接進入各個submodule commit。但要記得，main module也要commit才會連上最新的submodule。
```bash
cd SUBMODULE_FOLDER
# make some change
git add -u && git commit -m 'update in submodule only'
# remeber to push submodule to origin
cd MAIN_FOLDER
git add -u && git commit -m 'update main module to ref new submodule commit'
# remeber to push main module to origin
```

不過筆者還是建議大家分開使用。不要全部混在一起，因為太多同步問題會漏。

## 那些坑

### submodule 設定
有些時候，我們並不想追蹤submodule的預設分支。對於初次新增時，我們可以

```bash
git submodule add -b YOUR_BRANCH REPO_URL_OR_RELATIVE_REPO_PATH
git submodule add -b feature/devcontainer https://github.com/macauyeah/spring-boot-multiple-datasource.git
git submodule add -b feature/devcontainer ../spring-boot-multiple-datasource
```

若在初始化後期，想改branch，可以直接修改設定檔。(首次做，還是建議使用指令方式加入，因為第一次總要把submodule整個歷史記錄取下來。)
```
# file .gitmodules
[submodule "spring-boot-multiple-datasource"]
	path = spring-boot-multiple-datasource
	url = https://github.com/macauyeah/spring-boot-multiple-datasource.git
	branch = YOUR_BRANCH
```

關於上述 `url` 的部份，如果是公開的倉庫，當然可以以完整的方式存取。例如你可以直寫 `url = https://github.com/macauyeah/spring-boot-multiple-datasource.git`。

若為私有倉庫，道理上要本機有權限存取才行，對於持續整合/持續部署就有些麻煩。正常解決方向就是 CI Server 有齊所有倉庫的存取權限，具體要根據不同 CI Server 的設定，有時候還要跨 Docker 的方式去接入。那是有夠麻煩的一件事。但若果 main module 與 sub module 剛好為同一個倉，我們也可以使用相對路勁來解決。

```
# file .gitmodules
[submodule "spring-boot-multiple-datasource"]
	path = spring-boot-multiple-datasource
	url = ../spring-boot-multiple-datasource.git
	branch = YOUR_BRANCH
```

但這是有代價的，我們在本地 checkout 時，也必需要模疑類似的文件夾架構，也就是 sub module 也要獨立 checkout 。

### draft CI 參數
壞處，ci version overwrite，