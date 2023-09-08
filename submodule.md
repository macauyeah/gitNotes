# git submodule
初次實務上使用submodule來同時管理幾個project的更新。如果有任何理解上的錯誤，請在github中提issue或pull request。

# why submodule
假設你的團隊中有三個人，A君做A Project，B君做B Project，C君做Main Project。如果可以，A，B各提供已編譯的Binary或Library，給C君直接使用就最好。

但要做到好好管理，A，B都要有自己的發佈系統，即是把Binary上傳到某個分享Repo中，這樣C君就能有條理地通過IDE或Compile工具下載對應的版本。如果是javascript，Repo可能就是npm repo，如果是java，可能就是maven repo。這亦代表A，B君對程式編譯、打包、版本命名等都要很熟悉，不能一輩子都v1.0.0。

如果團隊對這些都不熟悉，C君靠著Submodule的功能，也可以硬把A，B的Source code取出，靠C君做最後打包。同理，如果大家要連結多個沒有發佈系統的文字資料，也

init add submodule in your current project
```
cd SOMEWHREE_IN_MAIN_FOLDER
git submodule add SUBMODLE_REPO_LINK
cd MAIN_FOLDER
git add -u && git commit -m 'Add sub module'
```

```
cd MAIN_FOLDER
git submodule update --remote
git add -u && git commit -m 'update sub module and auto checkout'
```